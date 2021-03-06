---
title: "我的世界MCDR+Fabric服务器搭建"
date: 2022-08-05T08:00:00+08:00
tags: ["ArchLinux","Minecraft","MCDReforged","Fabric","Game Server"]
categories: ["ArchLinux"]
draft: false
---

## 1.配置环境

安装JDK Python pip ...  
`paru -S liberica-jdk-full-bin python python-pip minecraft-server fabric-server`  
安装MCDR  
`pip install mcdreforged`

## 2.搭建原版服务端

创建MC/server文件夹  
`mkdir -p MC/server`  
进入MC/server文件夹  
`cd MC/server`  
运行服务端  
`minecraftd start`  
此时会报错一次 问你是否同意协议 修改eula.txt文件 把false改为true  
`sudo nvim /srv/minecraft/eula.txt`
`eula=true`  
再次运行服务端  
`minecraftd start`  
出现 Done 说明服务器搭建成功  
输入 stop 停止服务器  
可以通过[server.properties](https://minecraft.fandom.com/wiki/Server.properties)文件对服务器进行一些配置  
要调整默认设置(例如最大RAM 线程数等)  
`sudo nvim /etc/conf.d/minecraft`

## 3.搭建Fabric服务端

运行Fabric服务端以生成一些文件  
`quiltd start`  
出现 Done 说明服务器搭建成功  
输入 stop 停止服务器

## 4.搭建MCDR

把/srv/minecraft/文件软连接到MC/server文件夹  
`sudo ln -s /srv/minecraft/ /home/username/MC/server`  
返回上层目录(MC)  
`cd ..`  
初始化MCDR环境  
`python -m mcdreforged init`  
启动MCDR  
`python -m mcdreforged`  
MCDR+Fabric服务端搭建成功

## 也可以看看

[原版服务器设置文件Wiki](https://minecraft.fandom.com/wiki/Server.properties)  
[MCDReforged的文档](https://mcdreforged.readthedocs.io/en/latest/index.html)  
[MCDReforged的插件目录索引](https://github.com/MCDReforged/PluginCatalogue/blob/catalogue/readme.md)  
[Minecraft的ArchWiki](https://wiki.archlinux.org/title/minecraft#Introduction)  
MC1.11.2前后 离线服务器也会使用正版验证的UUID 如果你的离线服务器出现服务器白名单添加玩家后依旧进不去的问题
你可能需要这个[模组](https://www.curseforge.com/minecraft/mc-mods/easywhitelist)