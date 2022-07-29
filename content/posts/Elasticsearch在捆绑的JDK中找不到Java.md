---
title: "Elasticsearch在捆绑的JDK中找不到Java"
date: 2022-07-29T14:00:03+08:00
tags: ["ArchLinux","故障排除","Elasticsearch","Java"]
categories: ["ArchLinux"]
draft: false
---

报错:  
`could not find java in bundled JDK at /usr/share/elasticsearch/jdk/bin/java`  
解决办法:  
`sudo ln -snf /usr/lib/jvm/default /usr/share/elasticsearch/jdk`

## 也可以看看

[Elasticsearch的文档](https://www.elastic.co/guide/index.html)  
[Elasticsearch的ArchWiki](https://wiki.archlinux.org/title/Elasticsearch)  
[Elasticsearch文档的设置部分](https://www.elastic.co/guide/en/elasticsearch/reference/current/setup.html)