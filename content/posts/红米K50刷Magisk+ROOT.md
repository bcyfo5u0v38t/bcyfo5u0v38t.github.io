---
title: "红米K50刷Magisk"
date: 2022-07-30T08:00:00+08:00
tags: ["Linux","K50","Phone","Magisk","ROOT"]
draft: false
---

## 1.解锁BootLoader

手机进入 设置>开发者选项>设备解锁状态 中绑定账号和设备  
电脑打开申请[解锁小米手机](https://www.miui.com/unlock/index.html)下载解锁工具(只有Windows版)  
进入Bootloader模式(重启 按住音量下键)  
通过USB连接手机 打开解锁工具登陆并点击解锁按钮  
解锁成功(账号绑定手机必须满七天)

## 2.线刷

从[XiaomiROM](https://xiaomirom.com/rom/redmi-k50-rubens-china-fastboot-recovery-rom/)下载线刷包并提取boot.img  
下载[Magisk](https://github.com/topjohnwu/Magisk/releases)到手机并安装  
打开Magisk 安装>选择并修补一个文件>选择刚刚提取的boot.img  
修补结束会生成一个名字为 magisk_patched-版本号_随机字符.img 的文件每次生成的文件名都不一样  
手机连接到电脑 把boot.img和magisk_patched-版本号_随机字符.img 两个文件复制到电脑  
安装安卓工具包  
`paru -S android-tools`  
重启到Fastboot模式(重启 按住电源键和音量下键)  
刷入面具  
`fastboot flash boot magisk_patched-版本号_随机字符.img`  
重启手机

## 也可以看看

[Magisk的官方文档](https://topjohnwu.github.io/Magisk)  
[Android的ArchWiki](https://wiki.archlinux.org/title/android)  