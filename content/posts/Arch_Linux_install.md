---
title: "Arch Linux 安装(GPT+UEFI)"
date: 2022-07-18T08:00:00+08:00
draft: false
---

# 安装前的准备

## 1.确保网络环境

Arch Linux的安装需要网络  
有线网络:使用路由器分接出来的网线(也可以使用手机USB网络共享) 以DHCP的方式直接上网  
WiFi无线网络:建议事先把自己所用的WiFi名称改成自己能记住的英文名称 因为安装时无法显示和输入中文名的WiFi 你会看到一堆不知道是什么的方块 虽然通过一些繁琐的步骤可以解决终端中文的问题 但是显然这么做在安装Arch
Linux时毫无必要  
有些笔记本电脑上存在无线网卡的硬件开关或者键盘控制 开机后安装前需要确保你的无线网卡硬件开关处于打开状态

## 2.刻录启动U盘

准备一个2G以上的优盘 刻录一个安装启动盘 安装镜像 iso 在[下载页面](https://archlinux.org/download/)下载 你需要选择通过磁力链接或使用torrent下载  
也可以下载[Arch Linux GUI](https://sourceforge.net/projects/arch-linux-gui/) 一个快速 离线 图形化的安装程序的Arch Linux(本教程使用此镜像)  
建议使用[Ventoy](https://www.ventoy.net/cn/index.html)来进行U盘刻录 在[GitHub Releases](https://github.com/ventoy/Ventoy/releases)
页面下载

## 3.进入主板 BIOS 进行设置

插入优盘并开机 在开机的时候 按下F2/F8/F10/DEL等(取决与你的主板型号 具体请查阅你主板的相关信息)按键 进入主板的BIOS设置界面  
关闭安全启动:在类似名为security的选项卡中 找到一项名为Secure Boot(名称可能略有差异)的选项 选择Disable将其禁用  
调整启动方式为UEFI:在某些旧的主板里 需要调整启动模式为UEFI 而非传统的 BIOS/CSM 在类似名为boot的选项卡中 找到类似名为Boot Mode的选项 确保将其调整为UEFI only 而非legacy/CSM  
调整硬盘启动顺序:在类似名为boot的选项卡中 找到类似名为Boot Options(名称可能略有差异)的设置选项 将USB优盘的启动顺序调至首位(一些主板需要手动选择 无需设置硬盘启动顺序)  
开启虚拟化功能:在类似名为Configuration或Security的选项卡 找到类似名为Virtualization(名称可能略有差异)的设置选项 开启Intel处理器VT-x或对于AMD处理器AMD-V功能
Intel处理器可能还需要开启iommu功能

## 4.准备安装

最后保存BIOS设置并退出 一般的按键是F10 此时系统重启 不出意外你应该已经进入Arch Linux的安装界面

# 开始安装

NVIDIA显卡请选择带有NVIDIA字样的启动项  
Arch Linux GUI的终端默认不是root权限 切换到root  
`sudo su`

## 1.禁用 reflector

reflector会为你选择速度合适的镜像源 但其结果并不准确 同时会清空配置文件中的内容 后面会手动设置镜像源 这里先禁用  
`systemctl stop reflector.service`

## 2.再次确保是否为UEFI模式

`ls /sys/firmware/efi/efivars`  
若输出了一堆东西 即EFI变量 则说明已在UEFI模式 否则请确认你的启动方式是否为UEFI

## 3.连接网络

对于有线连接来说 直接插入网线即可  
对于无线连接 则需进行如下操作进行网络连接 也可以直接右上角选择WiFi进行连接  
无线连接使用iwctl命令进行 按照如下步骤进行网络连接

```
iwctl                           #执行iwctl命令，进入交互式命令行
device list                     #列出设备名，比如无线网卡看到叫 wlan0
station wlan0 scan              #扫描网络
station wlan0 get-networks      #列出网络 比如想连接SSR这个无线
station wlan0 connect SSR       #进行连接 输入密码即可
exit                            #成功后exit退出
```

使用PPPoE拨号:  
`pppoe-setup`  
USER NAME 输入拨号登录账号  
INTERFACE 输入网卡名称  
Enter the demand value 直接回车  
DNS 输入8.8.8.8，会让你输入第二个DNS，直接回车就好  
PASSWORD 输入你的拨号登录密码  
firewall 设置为0  
最后确认输入y回车  
`pppoe-start`

可以等待几秒等网络建立链接后再进行下面测试网络的操作  
`ping baidu.com`

如果你不能正常连接网络，首先确认系统已经启用网络接口

```
ip link  #列出网络接口信息，如不能联网的设备叫wlan0
ip link set wlan0 up #比如无线网卡看到叫 wlan0
```

如果随后看到类似Operation not possible due to RF-kill的报错 继续尝试rfkill命令来解锁无线网卡  
`rfkill unblock wifi`

## 4.更新系统时钟

```
timedatectl set-ntp true    #将系统时间与网络时间进行同步
timedatectl status          #检查服务状态
```

## 5.分区

这是我的分区方案:  
EFI分区/dev/nvme0n1p1: /efi 100M  
根(root)目录/dev/nvme0n1p2: / 128G  
家目录/dev/nvme0n1p3: /home 剩余全部  
swap分区/dev/nvme0n1p4: 8G 现在的内存大小很少使用到swap 但是如果你需要休眠 那swap是必须的(
你也可以使用[交换文件](https://wiki.archlinux.org/title/Swap#Swap_file) 不建议)  
使用fdisk进行分区 这里假设你使用NVME的固态硬盘 名称为nvme0n1 如果你是机械硬盘 那可能是sdx  
一般建议将EFI分区设置为磁盘的第一个分区 据说有些主板如果不将EFI设置为第一个分区 可能有不兼容的问题  
m : 显示菜单和帮助信息  
g : 新建GPT分区表  
n : 新建分区(如果已有标识 会有提示 输入y回车继续即可)  
t : 设置分区号(1:EFI System 其他分区使用默认的Linux filesystem即可无需设置)  
w : 保存修改

```
fdisk -l    #列出所有分区表
fdisk /dev/nvme0n1  #对/dev/nvme0n1进行分区
```  

## 6.格式化

格式化命令要与上一步分区中生成的分区名字对应才可以

```
mkfs.fat -F32 /dev/nvme0n1p1    #格式化efi分区
mkfs.btrfs /dev/nvme0n1p2    #格式化根目录
mkfs.btrfs /dev/nvme0n1p3    #格式化家目录
mkswap /dev/nvme0n1p4    #格式化swap
swapon /dev/nvme0n1p4   #启用swap
```

## 7.挂载

在挂载时 挂载是有顺序的 先挂载根分区 再挂载EFI分区  
这里是固态的挂载选项 如果是机械硬盘 应该删除discard=async和ssd这两个挂载选项

```
mount -t btrfs -o discard=async,autodefrag,compress=zstd,ssd /dev/nvme0n1p2 /mnt    #挂载根目录
mkdir /mnt/efi  #创建efi目录
mount /dev/nvme0n1p1 /mnt/efi   #挂载efi目录
mkdir /mnt/home    #创建home目录
mount -t btrfs -o discard=async,autodefrag,compress=zstd,ssd /dev/nvme0n1p3 /mnt/home    #挂载家目录
```

## 8.镜像源的选择

使用如下命令编辑镜像列表:
`vim /etc/pacman.d/mirrorlist`
其中的首行是将会使用的镜像源 添加中科大或清华的放在最上面即可

```
Server = https://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch
```

## 9.安装系统

安装基础包和一些功能性软件  
`pacstrap /mnt base base-devel linux-lts linux-lts-headers linux-zen linux-zen-headers linux-firmware neovim bash-completion`  
base-devel在AUR包的安装是必须的 linux-zen高性能内核 linux-lts稳定内核用来备用 neovim一个编辑器 bash-completion一个bash补全工具

## 10.生成fstab文件

fstab用来定义磁盘分区  
`genfstab -U /mnt >> /mnt/etc/fstab`  
复查一下/mnt/etc/fstab确保没有错误  
`cat /mnt/etc/fstab`

## 11.change root

把环境切换到新系统的/mnt下  
`arch-chroot /mnt`

## 12.时区设置

设置时区 在/etc/localtime下用/usr中合适的时区创建符号连接 如下设置上海时区  
`ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime`  
随后进行硬件时间设置 将当前的正确UTC时间写入硬件时间  
`hwclock --systohc`

## 13.设置 Locale 进行本地化

Locale 决定了地域 货币 时区日期的格式 字符排列方式和其他本地化标准

使用neovim编辑/etc/locale.gen 去掉en_US.UTF-8所在行以及zh_CN.UTF-8所在行的注释符号(#)  
`nvim /etc/locale.gen`  
然后使用如下命令生成 locale:  
`locale-gen`  
最后向/etc/locale.conf导入内容(不要在这里设置中文)  
`echo 'LANG=en_US.UTF-8'  > /etc/locale.conf`

## 14.设置主机名

首先在/etc/hostname设置主机名  
`echo 'ArchLinux' > /etc/hostname`  
''中输入你想为主机取的主机名 这里比如叫ArchLinux  
接下来在/etc/hosts设置与其匹配的条目  
`nvim /etc/hosts`  
加入如下内容

```
127.0.0.1   localhost
::1         localhost
127.0.1.1   ArchLinux
```

## 15.配置Pacman 添加cn源

ILoveCandy吃豆人彩蛋  
ParallelDownloads = 5并行下载  
去掉Color所在行以及ParallelDownloads = 5所在行的注释符号(#)  
在Color下一行加上ILoveCandy  
`nvim /etc/pacman.conf`  
在文件最下面加上cn源

```
[archlinuxcn]
Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
```

导入GPG key  
`pacman -Sy archlinuxcn-keyring`  
如果出现报错可以执行以下命令  
`rm -rf /etc/pacman.d/gnupg && pacman-key --init && pacman-key --populate archlinux archlinuxcn`

## 16.安装微码 引导程序 桌面环境 Libvirt全家桶...

```
pacman -S usb_modeswitch
amd-ucode
intel-ucode
mesa vulkan-intel
mesa xf86-video-amdgpu vulkan-radeon libva-mesa-driver mesa-vdpau
nvidia-dkms nvidia-settings cuda
grub efibootmgr fish qemu-desktop libvirt edk2-ovmf virt-manager dnsmasq ebtables iptables-nft dmidecode bridge-utils openbsd-netcat openssh firewalld dhcpcd paru v2raya v2ray fcitx5-im fcitx5-chinese-addons obs-studio v4l2loopback-dkms tlp plasma plasma-wayland-session konsole dolphin bluez bluez-utils pipewire-pulse wireplumber archlinux-wallpaper ttf-sarasa-gothic lxd vifm ria2 telegram-desktop gwenview mpv ark neofetch kdeconnect scrcpy flameshot screenkey bless  
```

微码

```
pacman -S intel-ucode   #Intel的CPU
pacman -S amd-ucode     #AMD的CPU
```

引导程序(os-prober让grub-mkconfig可以搜索其他已安装的系统并自动将它们添加到grub菜单中 需要挂载其他系统引导的分区 单Arch Linux 不需要os-prober):grub efibootmgr os-prober  
KDE Plasma桌面环境(plasma-wayland-session使用Wayland启动Plasma 仍存在一些缺失的功能和已知问题 建议使用xorg):plasma plasma-wayland-session konsole dolphin ttf-sarasa-gothic archlinux-wallpaper  
显卡驱动  
IntelGPU:mesa vulkan-intel  
AMDGPU:mesa xf86-video-amdgpu vulkan-radeon libva-mesa-driver mesa-vdpau  
NVIDIAGPU:nvidia-dkms nvidia-settings cuda

## 17.新建一个非root用户

wheel:sudo权限 lp:使用蓝牙 video:OBS录屏及虚拟摄像头  
`useradd -m -G wheel,libvirt,video,lxd,lp -s /usr/bin/fish user1`  
设置用户密码  
`passwd user1`  
设置root密码  
`passwd root`  
设置sudo权限 去掉%wheel ALL=(ALL) NOPASSWD: ALL所在行的注释符号(#)  
`EDITOR=nvim visudo`

## 18.安装并生成GRUB配置文件

安装grub  
`grub-install --target=x86_64-efi --efi-directory=/efi`  
修改grub配置  
`nvim /etc/default/grub`  
NVIDIA显卡:nvidia-drm.modeset=1 AMDCPU:amd_iommu=on (显卡直通需要intel_iommu=on iommu=pt
pcie_acs_override=downstream,multifunctiom)  
`GRUB_CMDLINE_LINUX_DEFAULT="loglevel=5 nowatchdog nvidia-drm.modeset=1 intel_iommu=on iommu=pt pcie_acs_override=downstream,multifunctiom"`  
如果后面重启后分辨率不正确 可以设置分辨率  
`GRUB_GFXMODE=2560x1440,auto`  
生成GRUB配置文件  
`grub-mkconfig -o /boot/grub/grub.cfg`

## 19.一些配置

去掉Bottomup和  
[bin]  
FileManager = vifm  
所在行的注释符号(#)  
`nvim /etc/paru.conf`  
防止开关机等待90秒bug  
`nvim /etc/systemd/system.conf`  
去掉所在行的注释符号(#)并改成下面这样

```
DefaultTimeoutStartSec=10s  
DefaultTimeoutStopSec=10s  
DefaultRestartSec=100ms
```  

设置默认编辑器 中文环境 fcitx环境变量  
`nvim /etc/environment`
把下面添加进去

```
EDITOR='nvim'
LANG=zh_CN.UTF-8
LANGUAGE=zh_CN:en_US
GTK_IM_MODULE=fcitx
QT_IM_MODULE=fcitx
XMODIFIERS=@im=fcitx
SDL_IM_MODULE=fcitx
INPUT_METHOD=fcitx
GLFW_IM_MODULE=fcitx
```

修改编译所用线程  
`nvim /etc/makepkg.conf`  
去掉所在行的注释符号(#) -j后面是根据自己cpu线程数填 比如128核256线程   
`MAKEFLAGS="-j256"`

## 20.启动服务并重启

设置一些服务开机自启动

`systemctl enable sddm NetworkManager bluetooth tlp v2raya firewalld libvirtd sshd lxd.service`  
退出  
`exit`  
重启  
`reboot`  
等待全黑后拔掉U盘

## 21.安装后的一些 比如在live里不咋方便弄的

启动默认虚拟网络并设为开机自启  
`sudo virsh net-start default && sudo virsh net-autostart default`  
安装谷歌浏览器  
`paru -S google-chrome`  
V2RayA(V2Ray的WEBUI)地址  
`http://localhost:2017/`  
配置fishshell通过WEBUI  
`fish_config`  
fishshell可以从手册页生成自动补全  
`fish_update_completions`  
MPV复制带有默认设置的示例配置文件  
`cp -r /usr/share/doc/mpv/ ~/.config/`  
修改vifm配置(你可能需要先运行一次vifm)  
`nvim ~/.config/vifm/vifmrc`  
把set vicmd=vim改成set vicmd=nvim

## 也可以看看

[Arch Linux 安装使用教程 - ArchTutorial - Arch Linux Studio](https://archlinuxstudio.github.io/ArchLinuxTutorial/#/)  
[LEDIS的博客](https://liucreator.gitlab.io/zh/)  
[Arch Linux Wiki](https://wiki.archlinux.org/)  
[V2RayA文档](https://v2raya.org/)  
[fish shell文档](https://fishshell.com/docs/current/)  
[LXD文档](https://linuxcontainers.org/lxd/docs/master/)