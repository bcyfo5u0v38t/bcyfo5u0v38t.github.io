---
title: "使用Docker+Calibre-Web搭建数字图书馆"
date: 2022-07-21T08:00:00+08:00
draft: false
---

## 1.下载镜像

`docker pull johngong/calibre-web:latest`  
列出本地镜像  
`docker images`

## 2.启动容器

```
docker run -d \
--name=my-ebook-library \
-p 22331:8083 \
-v /home/username/.config/calibre/:/config \
-v /home/username/Calibre-Library/:/library \
-v /home/username/Downloads/autoaddbooks:/autoaddbooks \
-e CALIBRE_SERVER_USER=username \
-e CALIBRE_SERVER_PASSWORD=password \
-e CALIBRE_ASCII_FILENAME=false \
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