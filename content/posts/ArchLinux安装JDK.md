---
title: "ArchLinux安装JDK"
date: 2022-08-04T08:00:00+08:00
tags: ["Linux","JDK","Java"]
draft: false
---

## 安装JDK

安装最新的JDK版本  
`paru -S liberica-jdk-full-bin`  
还可以安装其他版本的JDK 比如8 11 17版本  
`paru -S liberica-jdk-8-full-bin liberica-jdk-11-full-bin liberica-jdk-17-full-bin`

## 2.配置环境变量

安装后会自动配置环境变量 无需手动配置  
可以使用 archlinux-java 来切换JDK版本  
查看Java环境变量  
`archlinux-java status`  
切换到JDK8  
`sudo archlinux-java liberica-jdk-8-full`  
链接/usr/lib/jvm/default和/usr/lib/jvm/default-runtime应始终使用 archlinux-java

## 也可以看看

[Java的ArchWiki](https://wiki.archlinux.org/title/java)  
[Oracle的文档](https://docs.oracle.com/en/java/javase/)