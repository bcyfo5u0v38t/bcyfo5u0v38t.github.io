---
title: "安装USB无线网卡MW300UH驱动"
date: 2022-08-04T08:00:00+08:00
tags: ["ArchLinux","MW300UH","8192fu","Wireless","Driver"]
categories: ["ArchLinux"]
draft: false
---

## 1.查看USB设备信息

`sudo lsusb`  
通过ID 0bda:f192 浏览器搜索得知对应驱动是 8192fu

## 2.安装驱动

`paru -S rtl8192fu-dkms-git`

## 3.USB无线网卡工作模式切换

一些所谓的免驱USB无线网卡 可能会被识别成U盘...  
安装USB_ModeSwitch  
`paru -S usb_modeswitch`  
切换到无线网卡模式  
`sudo usb_modeswitch -KW -v 0bda -p a192`

## 也可以看看

[无线网络配置的ArchWiki](https://wiki.archlinux.org/title/Network_configuration/Wireless)