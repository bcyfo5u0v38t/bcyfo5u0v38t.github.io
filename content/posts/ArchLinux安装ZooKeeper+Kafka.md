---
title: "ArchLinux安装ZooKeeper+Kafka"
date: 2022-08-12T08:00:00+08:00
tags: ["Linux","ZooKeeper","Kafka","Message queue"]
categories: ["Linux"]
draft: false
---

## 1.安装ZooKeeper+Kafka

安装Kafka的同时会自动安装ZooKeeper  
`paur -S kafka`

## 2.启用并启动Kafka服务

这同时会自动启动并启动zookeeper@kafka.service  
`systemctl enable --now kafka.service`

## 也可以看看

[ZooKeeper的文档](https://zookeeper.apache.org/doc/current/index.html)  
[Kafka的文档](https://kafka.apache.org/quickstart)