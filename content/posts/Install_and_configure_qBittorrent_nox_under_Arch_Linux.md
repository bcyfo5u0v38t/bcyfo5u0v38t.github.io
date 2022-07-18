---
title: "Arch Linux下安装配置qBittorrent nox"
date: 2022-07-18T21:29:23+08:00
draft: false
---

## 1.安装qbittorrent-nox

`paru -S qbittorrent-nox`

## 2.初始化配置

`qbittorrent-nox`
会问你是否同意协议(y同意) 输入y 回车

## 3.WEBUI

qBittorrent的WEBUI应该在(浏览器打开)  
http://localhost:8080/  
默认用户名:admin  
密码:adminadmin

## 4.指定用户启动服务

回到服务器的命令行 Ctrl+c 退出qBittorrent nox  
指定用户开机启动服务(--now立刻启动)  
`sudo systemctl enable --now qbittorrent-nox@username`

## 也可以看看

[热门Tracker服务器列表](https://github.com/XIU2/TrackersListCollection)