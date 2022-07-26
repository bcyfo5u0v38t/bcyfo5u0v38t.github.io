---
title: "安装Tomcat"
date: 2021-09-25T08:00:00+08:00
tags: ["ArchLinux","Tomcat","Java"]
categories: ["ArchLinux"]
draft: false
---

## 1. 安装Tomcat

`paru -S tomcat10`

## 2.启用并启动Tomcat

`systemctl enable --now tomcat10.service`  
在浏览器 你的主机ip地址:8080 上验证安装

## 3.修改端口号

`sudo nvim /etc/tomcat10/server.xml`

```
<Connector port="22334" protocol="HTTP/1.1"
            connectionTimeout="20000"
            redirectPort="8443" />
```

## 4.添加一个新用户并设置密码

`sudo nvim /etc/tomcat10/tomcat-users.xml`

```
<role rolename="manager-gui"/>
<user username="用户名" password="密码" roles="manager-gui"/>
```

重启Tomcat服务 以使新的配置生效  
`sudo systemctl restart tomcat10.service`

## 5.把用户添加到组

`sudo gpasswd -a 用户名 tomcat10`  
验证是否添加成功  
`id 用户名`

## 也可以看看

[Tomcat的网站](https://tomcat.apache.org/)  
[Tomcat的ArchWiki](https://wiki.archlinux.org/title/Tomcat)