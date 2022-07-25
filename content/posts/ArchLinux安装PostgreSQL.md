---
title: "ArchLinux安装PostgreSQL"
date: 2022-07-27T08:00:00+08:00
tags: ["Linux","PostgreSQL","SQL"]
categories: ["Linux"]
draft: false
---

## 1.安装PostgreSQL

`paru -S postgresql`

## 2.BTRFS文件系统需要禁用写时复制

`sudo chattr +C /var/lib/postgres/data`

## 3.初始化PostgreSQL

`sudo su - postgres -c "initdb --locale=en_US.UTF-8 --encoding=UTF8 -D '/var/lib/postgres/data' --data-checksums"`

## 4.打开PostgreSQL的服务

`sudo systemctl start postgresql.service`

## 5.配置

切换到PostgreSQL用户   
`sudo su postgres`    
新建用户并设置权限 创建的用户和你的用户名一样就无需指定用户登录(这非常方便)   
`createuser --interactive`  
限制对数据库超级用户的访问权限   
`sudo nvim /var/lib/postgres/data/pg_hba.conf`

```
# TYPE  DATABASE        USER            ADDRESS                 METHOD

# "local" is for Unix domain socket connections only
local   all             postgres                                peer
```

使用Unix套接字  
`sudo nvim /var/lib/postgres/data/postgresql.conf`  
`listen_addresses = ''`

## 6.启用PostgreSQL的服务

回到有root权限的用户  
`exit`  
启用PostgreSQL服务  
`sudo systemctl enable postgresql.service`

## 也可以看看

[PostgreSQL的文档](https://www.postgresql.org/docs/)  
[PostgreSQL的ArchWiki](https://wiki.archlinux.org/title/PostgreSQL)
