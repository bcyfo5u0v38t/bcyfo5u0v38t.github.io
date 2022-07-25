---
title: "ArchLinux安装Elasticsearch"
date: 2022-07-20T08:00:00+08:00
tags: ["Linux","Elasticsearch","Search Engine"]
categories: ["Linux"]
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
解决办法 修改软连接  
`sudo ln -snf /usr/lib/jvm/default /usr/share/elasticsearch/jdk`

只配置一个节点 只允许在主机上访问并设置端口号  
`sudo nvim /etc/elasticsearch/elasticsearch.yml`

```
cluster.name: my-application
node.name: node-1
network.host: 127.0.0.1
http.port: 22333
discovery.seed_hosts: ["127.0.0.1"]
cluster.initial_master_nodes: ["node-1"]
```

修改内存使用量  
`sudo nvim /etc/elasticsearch/jvm.options.d/.options`

```
-Xms2g
-Xmx2g
```

## 4.启用并启动Elasticsearch

`sudo systemctl enable --now elasticsearch.service`

## 也可以看看

[Elasticsearch的文档](https://www.elastic.co/guide/index.html)  
[Elasticsearch的ArchWiki](https://wiki.archlinux.org/title/Elasticsearch)