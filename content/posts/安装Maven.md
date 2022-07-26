---
title: "安装Maven"
date: 2022-08-09T08:00:00+08:00
tags: ["ArchLinux","Maven"]
categories: ["ArchLinux"]
draft: false
---

## 1.安装Maven

`paru -S maven`

## 2.配置Maven镜像源

`sudo nvim /opt/maven/conf/settings.xml`

```
<mirror>
    <id>aliyunmaven</id>
    <mirrorOf>*</mirrorOf>
    <name>阿里云公共仓库</name>
    <url>https://maven.aliyun.com/repository/public</url>
</mirror>
```

## 也可以看看

[Maven的文档](https://maven.apache.org/guides/index.html)