---
title: "搭建MCDR加Fabric我的世界1.19服务器(Arch Linux)"
date: 2022-07-17T18:00:46+08:00
draft: false
---

## 第一步:配置环境

安装JDK Python pip  
`paru -S --needed liberica-jdk-full-bin python pip`  
安装MCDR  
`pip install mcdreforged`

## 第二步:搭建原版服务端

创建MC/server文件夹  
`mkdir -p MC/server`  
进入MC/server文件夹  
`cd MC/server`  
从麻将官网[下载](https://www.minecraft.net/en-us/download/server)原版服务端  
`curl https://launcher.mojang.com/v1/objects/e00c4052dac1d59a1188b2aa9d5a87113aaf1122/server.jar`  
运行服务端  
`java -jar server.jar nogui`  
此时会报错一次 修改文件夹中的eula.txt文件 将其内容改为  
`eula=false`  
再次运行服务端  
`java -jar server.jar nogui`  
出现`Done`说明服务器搭建成功  
输入`stop`停止服务器  
可以通过[server.properties](https://minecraft.fandom.com/wiki/Server.properties)文件对服务器进行一些配置

## 第三步:搭建Fabric服务端

从Fabric官网[下载Fabric服务端](https://fabricmc.net/use/server/)  
`curl -OJ https://meta.fabricmc.net/v2/versions/loader/1.19/0.14.8/0.11.0/server/jar`  
运行服务端以生成一些文件  
`java -jar fabric-server-mc.1.19-loader.0.14.8-launcher.0.11.0.jar nogui`  
出现`Done`说明服务器搭建成功  
输入`stop`停止服务器

## 第四步:搭建MCDR

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
MC1.11.2前后 离线服务器也会使用正版验证的UUID 如果你的离线服务器出现服务器白名单添加玩家后依旧进不去的问题
你可能需要这个[模组](https://www.curseforge.com/minecraft/mc-mods/easywhitelist)