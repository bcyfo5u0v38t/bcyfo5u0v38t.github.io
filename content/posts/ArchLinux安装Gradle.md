---
title: "ArchLinux安装Gradle"
date: 2022-08-09T08:00:00+08:00
tags: ["Linux","Gradle"]
draft: false
---

## 1.安装Gradle

`paru -S gradle`

## 2.配置Gradle镜像源

`sudo nvim 家目录/.gradle/init.gradle`

```
allprojects {
    repositories {
        maven { url 'https://maven.aliyun.com/repository/public/' }
        mavenLocal()
        mavenCentral()
    }
}
```

## 也可以看看

[Gradle的官方文档](https://docs.gradle.org/current/userguide/userguide.html)