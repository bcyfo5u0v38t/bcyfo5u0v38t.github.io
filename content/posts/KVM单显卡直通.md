---
title: "KVM单显卡直通"
date: 2022-07-23T15:07:20+08:00
tags: ["ArchLinux","KVM","GPU","Libvirt","QEMU"]
categories: ["ArchLinux"]
draft: false
---

## 1.配置一个KVM虚拟机

### 1.1启用IOMMU

重启进入UEFI/BIOS固件设置 开启IOMMU和虚拟化 通常在CPU高级选项里面 AMD处理器的一般叫Virtualization Technology或AMD-Vi
英特尔的叫VT-x或VT-d  
添加[内核参数](https://wiki.archlinux.org/title/Kernel_parameters)到引导程序  
AMD处理器 amd_iommu=on  
英特尔处理器 intel_iommu=on  
防止Linux接触无法通过的设备 iommu=pt  
如果您有不想通过的PCI设备 可以使用ACS补丁来隔离这些设备 pcie_acs_override=downstream,multifunction  
这样做会有[潜在风险](https://vfio.blogspot.com/2014/08/iommu-groups-inside-and-out.html)  
以GRUB为例  
`sudo nvim /etc/default/grub`  
`GRUB_CMDLINE_LINUX_DEFAULT="loglevel=5 nowatchdog nvidia-drm.modeset=1 intel_iommu=on iommu=pt pcie_acs_override=downstream,multifunctiom"`  
重启后 查看启动日志检查IOMMU是否已经启用  
`sudo dmesg | grep -e DMAR -e IOMMU`  
应当有类似的信息  
AMD处理器 amd_IOMMU:Detected  
英特尔处理器 Intel-IOMMU: enabled

### 1.2检查IOMMU组是否有效

运行脚本检查IOMMU组是否有效

```
#!/bin/bash
shopt -s nullglob
for g in $(find /sys/kernel/iommu_groups/* -maxdepth 0 -type d | sort -V); do
    echo "IOMMU Group ${g##*/}:"
    for d in $g/devices/*; do
        echo -e "\t$(lspci -nns ${d##*/})"
    done;
done;
```

或者使用这个脚本

```
#!/bin/bash

useColors=true
usePager=true

usage() {
	echo "\
Usage: $(basename $0) [OPTIONS]
Shows information about IOMMU groups relevant for working with PCI-passthrough

  -c -C 	enables/disables colored output, respectively
  -p -P 	enables/disables pager (less), respectively

  -h 		display this help message"
}

color() {
	if ! $useColors; then
		cat
		return
	fi

	rset=$'\E[0m'
	case "$1" in
		black) colr=$'\E[22;30m' ;;
		red) colr=$'\E[22;31m' ;;
		green) colr=$'\E[22;32m' ;;
		yellow) colr=$'\E[22;33m' ;;
		blue) colr=$'\E[22;34m' ;;
		magenta) colr=$'\E[22;35m' ;;
		cyan) colr=$'\E[22;36m' ;;
		white) colr=$'\E[22;37m' ;;
		intenseBlack) colr=$'\E[01;30m' ;;
		intenseRed) colr=$'\E[01;31m' ;;
		intenseGreen) colr=$'\E[01;32m' ;;
		intenseYellow) colr=$'\E[01;33m' ;;
		intenseBlue) colr=$'\E[01;34m' ;;
		intenseMagenta) colr=$'\E[01;35m' ;;
		intenseCyan) colr=$'\E[01;36m' ;;
		intenseWhite) colr=$'\E[01;37m' ;;
	esac

	sed "s/^/$colr/;s/\$/$rset/"
}

indent() {
	sed 's/^/\t/'
}

pager() {
	if $usePager; then
		less -SR
	else
		cat
	fi
}

while getopts cCpPh opt; do
	case $opt in
		c)
			useColors=true
			;;
		C)
			useColors=false
			;;
		p)
			usePager=true
			;;
		P)
			usePager=false
			;;
		h)
			usage
			exit
			;;
	esac
done

iommuGroups=$(find '/sys/kernel/iommu_groups/' -maxdepth 1 -mindepth 1 -type d)

if [ -z "$iommuGroups" ]; then
	echo "No IOMMU groups found. Are you sure IOMMU is enabled?"
	exit
fi

for iommuGroup in $iommuGroups; do
	echo "IOMMU group $(basename "$iommuGroup")" | color red

	for device in $(ls -1 "$iommuGroup/devices/"); do
		devicePath="$iommuGroup/devices/$device/"

		# Print pci device
		lspci -nns "$device" | color blue

		# Print drivers
		driverPath=$(readlink "$devicePath/driver")
		if [ -z "$driverPath" ]; then
			echo "Driver: none"
		else
			echo "Driver: $(basename $driverPath)"
		fi | indent | color cyan

		# Print usb devices
		usbBuses=$(find $devicePath -maxdepth 2 -path '*usb*/busnum')
		for usb in $usbBuses; do
			echo 'Usb bus:' | color cyan
			lsusb -s $(cat "$usb"): | indent | color green
		done | indent

		# Print block devices
		blockDevices=$(find $devicePath -mindepth 5 -maxdepth 5 -name 'block')
		for blockDevice in $blockDevices; do
			echo 'Block device:' | color cyan
			echo "Model: $(cat "$blockDevice/../model")" | indent | color green
			lsblk -no NAME,SIZE,MOUNTPOINT "/dev/$(ls -1 $blockDevice)" | indent | color green
		done | indent
	done | indent
done | pager
```

检查显卡是否自己的一个组里面

```
IOMMU group 11
        01:00.0 VGA compatible controller [0300]: NVIDIA Corporation TU104 [GeForce RTX 2060] [10de:1e89] (rev a1)
                Driver: nvidia
        01:00.1 Audio device [0403]: NVIDIA Corporation TU104 HD Audio Controller [10de:10f8] (rev a1)
                Driver: snd_hda_intel
```

复制 保存下来

### 1.3添加modprobe模块

ids=后面应该是刚才查看的显卡的那些id 每个值之间用 , 隔开  
`sudo nvim /etc/modprobe.d/vfio.conf`

```
options vfio-pci ids=10de:1e89,10de:10f8
options vfio-pci disable_idle_d3=1
options vfio-pci disable_vga=1
```

### 1.4安装需要的软件包

`paru -S qemu-desktop libvirt edk2-ovmf virt-manager dnsmasq ebtables iptables bridge-utils gnu-netcat openbsd-netcat`

### 1.5启用并启动服务

启动Libvirtd  
`sudo systemctl enable --now libvirtd.service`  
启动默认虚拟网络  
`sudo virsh net-start default && sudo virsh net-autostart default`  
打开 virt-manager 并设置一个基本虚拟机

## 2.设置Libvirt钩子

### 2.1设置Libvirt钩子

```
sudo mkdir /etc/libvirt/hooks
sudo nvim /etc/libvirt/hooks/qemu
```

```
#!/bin/bash

OBJECT="$1"
OPERATION="$2"

if [[ $OBJECT == "win10" ]]; then
	case "$OPERATION" in
        	"prepare")
                systemctl start libvirt-nosleep@"$OBJECT"  2>&1 | tee -a /var/log/libvirt/custom_hooks.log
                /bin/vfio-startup.sh 2>&1 | tee -a /var/log/libvirt/custom_hooks.log
                ;;

            "release")
                systemctl stop libvirt-nosleep@"$OBJECT"  2>&1 | tee -a /var/log/libvirt/custom_hooks.log  
                /bin/vfio-teardown.sh 2>&1 | tee -a /var/log/libvirt/custom_hooks.log
                ;;
	esac
fi
```

增加执行权限  
`sudo chmod +x /etc/libvirt/hooks/qemu`

### 2.2启动脚本

`sudo nvim /etc/libvirt/hooks/vfio-startup.sh`

```
#!/bin/bash
# Helpful to read output when debugging
set -x

long_delay=10
medium_delay=5
short_delay=1
echo "Beginning of startup!"

function stop_display_manager_if_running {
    # Stop dm using systemd
    if command -v systemctl; then
        if systemctl is-active --quiet "$1.service" ; then
            echo $1 >> /tmp/vfio-store-display-manager
            systemctl stop "$1.service"
        fi

        while systemctl is-active --quiet "$1.service" ; do
            sleep "${medium_delay}"
        done

        return
    fi

    # Stop dm using runit
    if command -v sv; then
        if sv status $1 ; then
            echo $1 >> /tmp/vfio-store-display-manager
            sv stop $1
        fi
    fi
}


# Stop currently running display manager
if test -e "/tmp/vfio-store-display-manager" ; then
    rm -f /tmp/vfio-store-display-manager
fi

stop_display_manager_if_running sddm
stop_display_manager_if_running gdm
stop_display_manager_if_running lightdm
stop_display_manager_if_running lxdm
stop_display_manager_if_running xdm
stop_display_manager_if_running mdm
stop_display_manager_if_running display-manager

sleep "${medium_delay}"

# Unbind VTconsoles if currently bound (adapted from https://www.kernel.org/doc/Documentation/fb/fbcon.txt)
if test -e "/tmp/vfio-bound-consoles" ; then
    rm -f /tmp/vfio-bound-consoles
fi
for (( i = 0; i < 16; i++))
do
  if test -x /sys/class/vtconsole/vtcon${i}; then
      if [ `cat /sys/class/vtconsole/vtcon${i}/name | grep -c "frame buffer"` \
           = 1 ]; then
	       echo 0 > /sys/class/vtconsole/vtcon${i}/bind
           echo "Unbinding console ${i}"
           echo $i >> /tmp/vfio-bound-consoles
      fi
  fi
done

# Unbind EFI-Framebuffer
if test -e "/tmp/vfio-is-nvidia" ; then
    rm -f /tmp/vfio-is-nvidia
fi

if lsmod | grep "nvidia" &> /dev/null ; then
    echo "true" >> /tmp/vfio-is-nvidia
    echo efi-framebuffer.0 > /sys/bus/platform/drivers/efi-framebuffer/unbind
fi

echo "End of startup!"
```

增加执行权限  
`sudo chmod +x /etc/libvirt/hooks/vfio-startup.sh`  
软链接到/bin目录下  
`sudo ln -s /etc/libvirt/hooks/vfio-startup.sh /bin/vfio-startup.sh`

### 2.3拆解脚本

`sudo nvim /etc/libvirt/hooks/vfio-teardown.sh`

```
#!/bin/bash
set -x

echo "Beginning of teardown!"

sleep 10

# Restart Display Manager
input="/tmp/vfio-store-display-manager"
while read displayManager; do
  if command -v systemctl; then
    systemctl start "$displayManager.service"
  else
    if command -v sv; then
      sv start $displayManager
    fi
  fi
done < "$input"

# Rebind VT consoles (adapted from https://www.kernel.org/doc/Documentation/fb/fbcon.txt)
input="/tmp/vfio-bound-consoles"
while read consoleNumber; do
  if test -x /sys/class/vtconsole/vtcon${consoleNumber}; then
      if [ `cat /sys/class/vtconsole/vtcon${consoleNumber}/name | grep -c "frame buffer"` \
           = 1 ]; then
    echo "Rebinding console ${consoleNumber}"
	  echo 1 > /sys/class/vtconsole/vtcon${consoleNumber}/bind
      fi
  fi
done < "$input"

# Rebind framebuffer for nvidia
if test -e "/tmp/vfio-is-nvidia" ; then
  echo "efi-framebuffer.0" > /sys/bus/platform/drivers/efi-framebuffer/bind
fi

echo "End of teardown!"
```

增加执行权限  
`sudo chmod +x /etc/libvirt/hooks/vfio-teardown.sh`  
软链接到/bin目录下  
`sudo ln -s /etc/libvirt/hooks/vfio-teardown.sh /bin/vfio-teardown.sh`

### 2.4Libvirt无睡眠服务

`sudo nvim /etc/systemd/system/libvirt-nosleep@.service`

```
[Unit]
Description=Preventing sleep while libvirt domain "%i" is running

[Service]
Type=simple
ExecStart=/usr/bin/systemd-inhibit --what=sleep --why="Libvirt domain \"%i\" is running" --who=%U --mode=block sleep infinity
```

增加执行权限  
`sudo chmod 644 -R /etc/systemd/system/libvirt-nosleep@.service`  
更改所有权  
`sudo chown root:root /etc/systemd/system/libvirt-nosleep@.service`

## 3配置Libvirt

### 3.1Libvirt

`sudo nvim /etc/libvirt/libvirtd.conf`

```
去掉#来取消注释这两行
unix_sock_group = "libvirt"
unix_sock_rw_perms = "0770"
#添加到文件的末尾
log_filters="1:qemu"
log_outputs="1:file:/var/log/libvirt/libvirtd.log"
```

### 3.2QEMU

`sudo nvim /etc/libvirt/qemu.conf`

```
user = "你的用户名"
group = "你的用户名"
```

把自己添加到libvirt组  
`sudo usermod -a -G libvirt 你的用户名`  
重启电脑或重启Libvirt  
`sudo systemctl restart libvirtd.service`

## 4.设置直通

### 4.1获取GPU VBIOS

方法1 在临时 Windows 10 上使用 GPU-Z  
方法2 或在TECHPOWERUP[下载](https://www.techpowerup.com/vgabios/)  
方法3  
AMD显卡  
`paru -S amdvbflash`  
`sudo amdvbflash -f -p 显卡位置 xxx.rom`  
NVIDIA显卡  
`paru -S nvflash`  
`sudo nvflash -b xxx.rom`

### 4.2给VBIOS打补丁(仅限NVIDIA)

用十六进制编辑器(Bless)打开rom  
寻找CHAR "video"删除之前的所有内容U.(十六进制中的55)  
保存在某个位置 比如  
`sudo mkdir /var/lib/libvirt/vbios`  
增加读取和执行权限  
`sudo chmod -R 660 xxx.rom`  
更改所有权  
`sudo chown 你的用户名:你的用户名 xxx.rom`

### 4.3配置虚拟机

转到virt-manager并将显卡的所有pcie部分添加到vm  
在pci设备中启用XML编辑 在显卡的所有pci设备里 </source>添加后

```
<rom bar="on" file="/path/to/patched.rom"/>
```

把信道 usb转接 显卡qxl 触摸板等东西删掉  
添加你的USB设备 比如鼠标 键盘和USB耳机...

### 4.4绕过Nvidia VM检测(或许从2021年4月起不再需要)

在`</hyperv>`前添加(value应该等于8-12个字母)

```
<vendor_id state='on' value='randomid'/>
```

AMD显卡可以用"AuthenticAMD"作为id但不需要 因为AMD阻止你显卡虚拟化  
在`<features>`和`</features>`之间添加

```
 <kvm>
 	<hidden state='on'/>
 </kvm>
```

## 也可以看看

[ledis的单显卡直通教程](https://gitlab.com/liucreator/LEDs-single-gpu-passthrough/-/blob/main/README-cn.md)  
[内核参数的ArchWiki](https://wiki.archlinux.org/title/Kernel_parameters)  
[通过OVMF的PCI直通的ArchWiki](https://wiki.archlinux.org/title/PCI_passthrough_via_OVMF)  
[QEMU的ArchWiki](https://wiki.archlinux.org/title/QEMU)  
[KVM的ArchWiki](https://wiki.archlinux.org/title/KVM)  
[Libvirt的ArchWiki](https://wiki.archlinux.org/title/libvirt)