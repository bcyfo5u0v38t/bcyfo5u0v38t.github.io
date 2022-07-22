---
title: "使用Docker+Calibre-Web搭建数字图书馆"
date: 2022-07-21T08:00:00+08:00
tags: ["Arch Linux","Docker","Calibre"]
draft: false
---

## 1.下载镜像

`docker pull johngong/calibre-web:latest`  
列出本地镜像  
`docker images`

## 2.启动容器

```
docker run -d \
--name=my-ebook-library \    设置容器名
-p 22331:8083 \    #把容器内8083(容器默认WEBUI访问端口)映射到22331
-v /home/username/.config/calibre:/config \    #配置文件位置
-v /home/username/Calibre-Library:/library \    #书库默认位置
-v /home/username/Downloads/autoaddbooks:/autoaddbooks \    #自动添加图书文件夹位置
-e CALIBRE_SERVER_USER=username \    #设置用户名
-e CALIBRE_SERVER_PASSWORD=password \    #设置密码
-e CALIBRE_ASCII_FILENAME=false \    #支持中文目录
johngong/calibre-web:latest
```

查看容器状态  
`docker ps`

## 3.浏览器访问

浏览器输入 服务器ip地址:端口 即可访问  
`http://localhost:22331`

## 也可以看看

[Calibre-Web的Docker镜像](https://hub.docker.com/r/johngong/calibre-web)  
[Docker官方文档](https://docs.docker.com/)