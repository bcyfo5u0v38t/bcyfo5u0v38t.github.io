---
title: "ArchLinux安装MariaDB"
date: 2022-07-19T08:00:00+08:00
draft: false
---

## 1.安装MariaDB

`paru -S mariadb`

## 2.BTRFS文件系统需要禁用写时复制

`sudo chattr +C /var/lib/mysql`

## 3.安装MariaDB

`sudo mariadb-install-db --user=mysql --basedir=/usr --datadir=/var/lib/mysql`

## 4.把用户添加到mysql用户组

`sudo usermod -a -G mysql username`

## 5.打开MariaDB的服务

`sudo systemctl start mariadb.service`

## 6.提高初始安全性

该mysql_secure_installation命令将交互式地引导您完成一些推荐的安全措施 例如删除匿名帐户和删除测试数据库:  
`sudo mysql_secure_installation`  
1问你root用户的密码 如果你刚刚安装了MariaDB 并且还没有设置root密码 你应该在这里按回车  
2问你是否切换到unix_socket身份验证 默认是y 回车即可  
3.问你是否更改root密码 默认是y 回车即可  
4.默认情况下 MariaDB安装有一个匿名用户 允许任何人 无需创建用户帐户即可登录MariaDB 问你是否移除匿名用户 默认是y 回车即可  
5.问你是否禁用远程root登录(如果你有从其他电脑连接数据库的需要 则应该输入n) 默认是y 回车即可  
6.默认情况下 MariaDB带有一个名为“test”的数据库 任何人都可以使用 问你是否移除这个测试数据库 默认是y 回车即可  
7.问你是否重新加载权限表将确保到目前为止所做的所有更改将立即生效(如果你反悔了 可以输入n重新来过) 默认是y 回车即可

## 7.开机自启MariaDB服务

`sudo systemctl enable mariadb.service`

## 也可以看看

[MariaDB的ArchWiki](https://wiki.archlinux.org/title/MariaDB)  
[MariaDB官方网站](https://mariadb.com/)  
[MariaDB知识库](https://mariadb.com/kb/en/)  
[MySQL性能调优脚本和专有技术](https://www.askapache.com/mysql/mysql-performance-tuning/)