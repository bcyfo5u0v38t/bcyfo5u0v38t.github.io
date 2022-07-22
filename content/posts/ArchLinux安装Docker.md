---
title: "ArchLinux安装Docker"
date: 2022-07-22T08:00:00+08:00
tags: ["Arch Linux","Docker"]
draft: false
---

## 1.安装Docker Docker-Compose

`paru -S docker docker-compose`  
把用户添加到docker组  
`sudo usermod -a -G docker username`

## 2.启用并启动Docker

`sudo systemctl enable --now docker.service`

## 也可以看看

[Docker的ArchWiki](https://wiki.archlinux.org/title/Docker)  
[Docker官方文档](https://docs.docker.com/)