---
title: "ArchLinux安装Docker无根模式"
date: 2022-07-23T08:00:00+08:00
draft: false
---

## 1.安装Docker(Rootless mode) Docker-Compose

`paru -S docker-rootless-extras docker-compose`

## 2.重映射用户和组

写入 你的用户名:165536:65536 到下面文件  
`sudo nvim /etc/subuid`  
写入你的用户名:165536:65536 到下面文件  
`sudo nvim /etc/subgid`

## 3.设置Docker Socket到环境变量

写入 DOCKER_HOST=unix://$XDG_RUNTIME_DIR/docker.sock 到下面文件  
`sudo nvim /etc/environment`

## 4.启用并启动Docker

`sudo systemctl enable --now docker.service`

## 也可以看看

[Docker的ArchWiki](https://wiki.archlinux.org/title/Docker)  
[Docker官方文档](https://docs.docker.com/)