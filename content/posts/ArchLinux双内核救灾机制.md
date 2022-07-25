---
title: "ArchLinux双内核救灾机制"
date: 2022-08-17T08:00:00+08:00
tags: ["Linux","Linux Kernel","Kexec","Kdump"]
draft: false
---

## 1.准备需要的软件包

`paru -S asp base-devel pacman-contrib kexec-tools makedumpfile modprobed-db`

## 2.准备内核源码

```
mkdir Kdump_Kernel
cd Kdump_Kernel
asp update linux-lts
asp export linux-lts
```

## 3.修改模板

修改参数以开启错误快照功能...  
当前默认的Arch内核版本已经设置了这些标志 可以通过查看/proc/config.gz来验证正在运行的内核是否设置了这些  
`nvim linux-lts/config #或config.x86_64`

```
CONFIG_DEBUG_INFO=y
CONFIG_CRASH_DUMP=y
CONFIG_PROC_VMCORE=y
```

或使用当前内核的配置  
`sudo zcat /proc/config.gz > .config`  
使用Modprobed-db删除不需要的模块  
创建配置文件  
`sudo modprobed-db`  
显示当前数据库模块  
`sudo modprobed-db list`  
使用当前加载的内核模块更新数据库  
`sudo modprobed-db store`  
启用并启动Modprobed-db服务 每小时更新一次数据库 以及启动和关机时  
`sudo systemctl enable --now modprobed-db.service`  
再编辑相同文件夹下的PKGBUILD 设置一个包名  
`nvim PKGBUILD`  
`pkgbase=linux-lts-kdump`

## 4.编译

更新MD5  
`updpkgsums`   
开始编译  
`makepkg -s --skippgpcheck`

## 5.安装自定义内核

Kdump_Kernel/linux-lts下面应该有两个.tar.xz文件  
使用 pacman -U 两个文件名 来安装

## 6.准备kdump-initramfs

修改默认的initramfs

```
--- mkinitcpio.conf
+++ mkinitcpio-kdump.conf
@@ -11,12 +11,12 @@
 # wish into the CPIO image.  This is run last, so it may be used to
 # override the actual binaries included by a given hook
 # BINARIES are dependency parsed, so you may safely ignore libraries
-BINARIES=()
+BINARIES=(/usr/bin/makedumpfile)

 # FILES
 # This setting is similar to BINARIES above, however, files are added
 # as-is and are not parsed in any way.  This is useful for config files.
-FILES=()
+FILES=(/etc/systemd/system/kdump-save.service)

 # HOOKS
 # This is the most important setting in this file.  The HOOKS control the
```

并更新 mkinitcpio 预设文件 以便在每次内核更新时重建新的 initrd

## 7.设置kdump内核

编辑引导加载程序配置 比如GRUB  
`sudo nvim /etc/default/grub`  
`GRUB_CMDLINE_LINUX_DEFAULT="crashkernel=512M@16M"    # 增加crashkernel参数`  
意思为从内存的16M地址位置开始 向后空出512M的内存区 这部分内存将被保留无法使用  
更新GRUB文件  
`sudo grub-mkconfig -o /boot/grub/grub.cfg`  
重启电脑  
`sudo reboot`  
告诉Kexec要使用的转储捕获内核  
`sudo kexec -p /boot/vmlinuz-linux-lts-kdump`  
如果需要 还可以指定内核 initramfs文件 根设备和其他参数   
`sudo kexec -p [/boot/vmlinuz-linux-kdump] --initrd=[/boot/initramfs-linux-kdump.img] --append="root=[root-device] single irqpoll maxcpus=1 reset_devices"`  
它将内核加载到保留区域 没有-p标志kexec会立即启动内核 但如果存在标志 内核将被加载到保留内存中 启动会推迟到崩溃

## 8.制作并启用启动Kdump服务

制作自动导出快照和重启的服务(不要启动这个服务)  
`sudo nvim /etc/systemd/system/kdump-save.service`

```
[Unit]
Description=Create dump after kernel crash
DefaultDependencies=no
Wants=local-fs.target
After=local-fs.target

[Service]
Type=idle
ExecStart=/bin/sh -c 'mkdir -p /var/crash/ && /usr/bin/makedumpfile -c -d 31 /proc/vmcore "/var/crash/crashdump-$$(date +%%F-%%T)"'
ExecStopPost=/usr/bin/systemctl reboot
UMask=0077
StandardInput=tty-force
StandardOutput=inherit
StandardError=inherit
```

通过这个服务 可以实现每当内核出现错误 Kdump内核启动时 将当前的环境快照保存到/var/crash/下 命名为crash-dump-日期时间 然后重启系统  
制作自动加载Kdump内核的服务  
`sudo nvim /etc/systemd/system/kdump.service`

```
[Unit]
Description=Load dump capture kernel
After=local-fs.target

[Service]
Type=oneshot
RemainAfterExit=true
ExecStart=/usr/bin/kexec -p [/boot/vmlinuz-linux-kdump] --initrd=[/boot/initramfs-linux-kdump.img] --append="root=[root-device] systemd.unit=kdump-save.service irqpoll maxcpus=1 reset_devices"
# convenience
ExecStartPost=/bin/sh -c 'mkdir -p /var/crash/ && /usr/bin/makedumpfile --dump-dmesg /proc/vmcore "/var/crash/crashdump-$$(date +%%F-%%T)".dmesg'
ExecStop=/usr/bin/kexec -p -u

[Install]
WantedBy=multi-user.target
```

启用并启动Kdump服务  
`sudo systemctl enable --now kdump.service`  
要检查崩溃内核是否已经加载  
`sudo cat /sys/kernel/kexec_crash_loaded`

## 也可以看看

[Linux内核文档的Kdump部分](https://docs.kernel.org/admin-guide/kdump/kdump.html)  
[Kdump的ArchWiki](https://wiki.archlinux.org/title/Kdump)  
[Kexec的ArchWiki](https://wiki.archlinux.org/title/kexec)  
[内核的ArchWiki](https://wiki.archlinux.org/title/Kernel#Compilation)  
[内核的传统编译部分ArchWiki](https://wiki.archlinux.org/title/Kernel/Traditional_compilation)  
[Modprobed-db的ArchWiki](https://wiki.archlinux.org/title/Modprobed-db)  
[GRUB的ArchWiki](https://wiki.archlinux.org/title/GRUB)  
[systemd的ArchWiki](https://wiki.archlinux.org/title/systemd)