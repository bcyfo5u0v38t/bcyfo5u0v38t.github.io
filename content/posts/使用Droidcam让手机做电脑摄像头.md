---
title: "使用Droidcam让手机做电脑摄像头"
date: 2022-07-27T08:00:00+08:00
tags: ["ArchLinux","Droidcam","Webcam"]
categories: ["ArchLinux"]
draft: false
---

## 1.安装Droidcam

`paru -S droidcam`  
手机端搜索安装droidcamx

## 2.载入所需模块

```
sudo modprobe videodev
sudo modprobe v4l2loopback
sudo modprobe v4l2loopback_dc 1920 1080   #分辨率
```

## 3.使用

将电脑和手机连入同一个局域网  
启动手机应用 会给出ip和端口号  
启动电脑端程序 将给出的ip和端口号填入 连接

## 也可以看看

[Droidcam的Github](https://github.com/dev47apps/droidcam)  
[Droidcam的ArchWiki](https://wiki.archlinux.de/title/Droidcam)  
[Droidcam的网站](https://www.dev47apps.com/)