---
title: "ArchLinux安装qBittorrent-enhanced-nox"
date: 2022-07-18T08:00:00+08:00
draft: false
---

## 1.安装qBittorrent-enhanced-nox

`paru -S qbittorrent-enhanced-nox`

## 2.初始化配置

运行qBittorrent-enhanced-nox  
`qbittorrent-nox`  
会问你是否同意协议(y同意) 输入y 回车  
如果提示端口被占用 可以指定端口  
`qbittorrent-nox --webui-port=22330`

## 3.WEBUI

qBittorrent的WEBUI应该在(浏览器打开)  
http://localhost:8080/  
默认用户名:admin  
密码:adminadmin

## 4.指定用户启用并启动服务

回到服务器的命令行 Ctrl+c 退出qBittorrent nox  
`sudo systemctl enable --now qbittorrent-nox@username`

## 也可以看看

[热门Tracker服务器列表](https://github.com/XIU2/TrackersListCollection)  
[qBittorrent官方Wiki](https://github.com/qbittorrent/qBittorrent/wiki)