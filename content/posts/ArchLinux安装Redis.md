---
title: "ArchLinux安装Redis"
date: 2022-07-28T08:00:00+08:00
tags: ["Linux","Redis","SQL"]
draft: false
---

## 1.安装Redis

`paru -S redis`

## 2.配置

禁用对 TCP 的监听   
`sudo nvim /etc/redis/redis.conf`  
`port 0`  
使用Unix套接字  
`unixsocket /run/redis/redis.sock`  
将套接字的权限设置为redis用户组的所有成员  
`unixsocketperm 770`

## 3.启用并启动Redis的服务

`sudo systemctl enable --now redis.service`

## 也可以看看

[Redis的官方文档](https://redis.io/docs)  
[Redis的ArchWiki](https://wiki.archlinux.org/title/redis)