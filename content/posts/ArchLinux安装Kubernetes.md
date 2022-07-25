---
title: "ArchLinux安装Kubernetes"
date: 2022-08-13T08:00:00+08:00
tags: ["Linux","kubernetes"]
draft: false
---

## 1.安装Kubernetes

`paru -S etcd kubernetes-control-plane kubernetes-node kubeadm containerd cri-o kubectl`

## 2.启用并启动kubelet服务

启动服务前应禁用Swap  
`sudo swapoff /dev/sdxy`  
`sudo systemctl enable --now kubelet.service`

## 也可以看看

[Kubernetes的官方文档](https://kubernetes.io/docs/home/)  
[Kubernetes的ArchWiki](https://wiki.archlinux.org/title/Kubernetes)