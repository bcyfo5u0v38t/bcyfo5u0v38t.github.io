---
title: "安装RabbitMQ"
date: 2022-08-18T08:00:00+08:00
tags: ["ArchLinux","RabbitMQ","Message queue"]
categories: ["ArchLinux"]
draft: false
---

## 1.安装RabbitMQ

`paru -S rabbitmq`

## 2.启用并启动RabbitMQ

`sudo systemctl enable --now rabbitmq.service`

## 3.启用WEB管理界面

`sudo rabbitmq-plugins enable rabbitmq_management`  
在 你的主机ip地址:15672 上访问管理界面  
默认凭据 username:guest password:guest

## 4.配置

指定主机名 主机名需要与hostname中的一致  
`sudo nvim /etc/rabbitmq/rabbitmq-env.conf`

```
NODENAME=主机名
NODE_IP_ADDRESS=0.0.0.0
NODE_PORT=5672
```

添加一个新用户并设置密码  
`sudo rabbitmqctl add_user 用户名 密码`  
设为管理员  
`sudo rabbitmqctl set_user_tags 用户名 administrator`

## 也可以看看

[RabbitMQ的文档](https://www.rabbitmq.com/documentation.html)  
[RabbitMQ的ArchWiki](https://wiki.archlinux.org/title/RabbitMQ)