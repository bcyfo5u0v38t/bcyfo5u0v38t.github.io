---
title: "ArchLinux安装Elasticsearch"
date: 2022-07-20T08:00:00+08:00
tags: ["Arch Linux","Elasticsearch","Search Engine"]
draft: false
---

## 1.配置环境

Elasticsearch需要JDK10以上版本 安装JDK  
注意 8.2(2022-07-18 14:16 (UTC))以上版本才可以使用JDK18构建  
`paru -S liberica-jdk-full-bin`  
确认环境是否正确  
`archlinux-java status`

## 2.安装Elasticsearch

`paru -S elasticsearch`

## 3.配置Elasticsearch

在启动Elasticsearch之前应该创建一个密钥库  
`sudo elasticsearch-keystore create`  
可能会有报错  
`could not find java in bundled JDK at /usr/share/elasticsearch/jdk/bin/java`  
解决办法 设置ES_JAVA_HOME变量  
`sudo nvim /etc/environment`  
把下面这条变量加进去  
`ES_JAVA_HOME=/usr/lib/jvm/default`

## 4.启用并启动Elasticsearch

`sudo systemctl enable --now elasticsearch.service`

## 也可以看看

[Elasticsearch的官方文档](https://www.elastic.co/guide/index.html)  
[Elasticsearch的ArchWiki](https://wiki.archlinux.org/title/Elasticsearch)