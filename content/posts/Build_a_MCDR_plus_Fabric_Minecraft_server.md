---
title: "搭建MCDR加Fabric我的世界1.19服务器(Arch Linux)"
date: 2022-07-17T18:00:46+08:00
draft: false
---

## 1.配置环境

安装JDK Python pip  
`paru -S --needed liberica-jdk-full-bin python pip minecraft-server fabric-server`  
安装MCDR  
`pip install mcdreforged`

## 2.搭建原版服务端

创建MC/server文件夹  
`mkdir -p MC/server`  
进入MC/server文件夹  
`cd MC/server`  
运行服务端  
`minecraftd start`  
此时会报错一次 修改文件夹中的eula.txt文件 将其内容改为  
`eula=false`  
再次运行服务端  
`minecraftd start`  
出现`Done`说明服务器搭建成功  
输入`stop`停止服务器  
可以通过[server.properties](https://minecraft.fandom.com/wiki/Server.properties)文件对服务器进行一些配置  
要调整默认设置(例如最大RAM 线程数等)
`sudo nvim /etc/conf.d/minecraft`

## 3.搭建Fabric服务端

运行Fabric服务端以生成一些文件  
`quiltd start`  
出现`Done`说明服务器搭建成功  
输入`stop`停止服务器

## 4.搭建MCDR

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
[Minecraft Arch Linux Wiki](https://wiki.archlinux.org/title/minecraft#Introduction)  
MC1.11.2前后 离线服务器也会使用正版验证的UUID 如果你的离线服务器出现服务器白名单添加玩家后依旧进不去的问题
你可能需要这个[模组](https://www.curseforge.com/minecraft/mc-mods/easywhitelist)