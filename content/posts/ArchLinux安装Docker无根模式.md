---
title: "ArchLinux安装Docker无根模式"
date: 2022-07-23T08:00:00+08:00
tags: ["Linux","Docker","Container"]
categories: ["Linux"]
draft: false
---

## 1.安装Docker(Rootless mode) Docker-Compose

`paru -S docker-rootless-extras docker-compose`

## 2.重映射用户和组

`sudo nvim /etc/subuid`  
`你的用户名:165536:65536`

`sudo nvim /etc/subgid`  
`你的用户名:165536:65536`

## 3.设置Docker Socket到环境变量

`sudo nvim /etc/environment`  
`DOCKER_HOST=unix://$XDG_RUNTIME_DIR/docker.sock`

## 4.重启Docker服务或启用并启动Docker服务

`sudo systemctl restart docker.service`  
`sudo systemctl enable --now docker.service`

## 也可以看看

[Docker的ArchWiki](https://wiki.archlinux.org/title/Docker)  
[Docker官方文档](https://docs.docker.com/)