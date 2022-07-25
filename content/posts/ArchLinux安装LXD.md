---
title: "ArchLinux安装LXD"
date: 2022-08-18T08:00:00+08:00
tags: ["Linux","LXD","Container"]
categories: ["Linux"]
draft: false
---

## 1. 安装LXD

`paru -S lxd`

## 2.用户添加到组

`sudo usermod -a -G lxd 用户名`

## 3.启用并启动LXD

`sudo systemctl enable --now lxd.service`

## 也可以看看

[LXD的ArchWiki](https://wiki.archlinux.org/title/LXD)
[LXD的文档](https://linuxcontainers.org/lxd/docs/master/)  
[Linux容器的ArchWiki](https://wiki.archlinux.org/title/Linux_Containers)