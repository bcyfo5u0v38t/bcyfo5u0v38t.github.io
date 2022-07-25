---
title: "ArchLinux双显卡切换"
date: 2022-08-14T08:00:00+08:00
tags: ["Linux","GPU","Optimus"]
categories: ["Linux"]
draft: false
---

## 1.安装optimus-manager optimus-manager-qt

`paru -S optimus-manager optimus-manager-qt`  
安装后 optimus-manager 守护进程应该已经自动启动 验证  
`sudo systemctl status optimus-manager.service`  
optimus-manager提供三种模式 分别为仅用独显 仅用核显 和 hybrid动态切换模式

## 2.动态切换模式

安装nvidia-prime  
`paru -S nvidia-prime`  
使用prime-run前缀来用独显运行某些程序  
`prime-run program`

## 也可以看看

[optimus-manager的Github](https://github.com/Askannz/optimus-manager)  
[NVIDIAOptimus的ArchWiki](https://wiki.archlinux.org/title/NVIDIA_Optimus)  
[PRIME的ArchWiki](https://wiki.archlinux.org/title/PRIME)