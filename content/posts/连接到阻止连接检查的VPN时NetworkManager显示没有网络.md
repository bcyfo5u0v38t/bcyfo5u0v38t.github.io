---
title: "连接到阻止连接检查的VPN时NetworkManager显示没有网络"
date: 2022-08-02T14:31:27+08:00
tags: ["ArchLinux","NetworkManager","VPN","故障排除"]
categories: ["ArchLinux"]
draft: false
---

## 1.禁用NetworkManager的连接检查

`sudo nvim /etc/NetworkManager/conf.d/20-connectivity.conf`

```
[connectivity] 
enabled=false
```

## 2.重启NetworkManager

`sudo systemctl restart NetworkManager`

## 也可以看看

[NetworkManager的ArchWiki](https://wiki.archlinux.org/title/NetworkManager)