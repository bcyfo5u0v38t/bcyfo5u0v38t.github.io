---
title: "ArchLinux安装LXD无根模式"
date: 2022-08-19T08:00:00+08:00
tags: ["Linux","LXD","Container"]
categories: ["Linux"]
draft: false
---

## 1.安装arch-install-scripts

`paru -S arch-install-scripts`

## 2.启用支持

`sudo nvim /etc/lxc/default.conf`

```
lxc.idmap = u 0 100000 65536
lxc.idmap = g 0 100000 65536
```

## 3重新映射用户和组

`sudo nvim /etc/subuid`  
`root:100000：65536`

`sudo nvim /etc/subgid`  
`root:100000：65536`

## 4.委派非特权Cgroups

`systemd-run --unit= myshell --user --scope -p "Delegate=yes" lxc-start container_name`  
或创建一个Systemd单元来委派非特权Cgroups  
`sudo nvim /etc/systemd/system/user@.service.d/delegate.conf`

```
[Service]
Delegate=cpu cpuset io memory pids
```

## 5.重启或启用并启动LXD服务

`sudo systemctl restart lxd.service`  
`sudo systemctl enable --now lxd.service`

## 也可以看看

[LXD的ArchWiki](https://wiki.archlinux.org/title/LXD)  
[LXD的文档](https://linuxcontainers.org/lxd/docs/master/)  
[Linux容器的ArchWiki](https://wiki.archlinux.org/title/Linux_Containers)  
[Cgroups的ArchWiki](https://wiki.archlinux.org/title/Cgroups)