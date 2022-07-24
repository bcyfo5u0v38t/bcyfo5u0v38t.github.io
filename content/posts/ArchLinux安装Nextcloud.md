---
title: "ArchLinux安装Nextcloud"
date: 2022-07-29T08:00:00+08:00
tags: ["Linux","Nextcloud","Storage"]
draft: false
---

## 1.安装Nextcloud 还需要MariaDB Nginx...

`paru -S nextcloud php-imagick php-intl php-fpm`

## 2.配置

复制/etc/php/php.ini到/etc/webapps/nextcloud  
`sudo cp /etc/php/php.ini /etc/webapps/nextcloud/php.ini`  
更改副本的所有权  
`chown nextcloud:nextcloud /etc/webapps/nextcloud/php.ini`  
配置PHP启用所需模块  
`sudo nvim /etc/webapps/nextcloud/php.ini`

```
extension=pdo_mysql
extension=bcmath
extension=bz2
extension=exif
extension=gd
extension=iconv
; in case you installed php-imagick (as recommended)
extension=imagick
; in case you also installed php-intl (as recommended)
extension=intl
date.timezone = Asia/Shanghai
memory_limit = 512M
open_basedir=/var/lib/nextcloud/data:/var/lib/nextcloud/apps:/tmp:/usr/share/webapps/nextcloud:/etc/webapps/nextcloud:/dev/urandom:/usr/lib/php/modules:/var/log/nextcloud:/proc/meminfo
```

配置Nextcloud  
`sudo nvim /etc/webapps/nextcloud/config/config.php`

```
'trusted_domains' =>
  array (
    0 => 'localhost',
    1 => 'cloud.example.org',
  ),    
'overwrite.cli.url' => 'https://cloud.example.org/',
'htaccess.RewriteBase' => '/',
```

示例主机名<cloud.example.com>  
设置环境变量  
`sudo nvim /etc/environment`  
`NEXTCLOUD_PHP_CONFIG=/etc/webapps/nextcloud/php.ini`  
临时设置  
`export NEXTCLOUD_PHP_CONFIG=/etc/webapps/nextcloud/php.ini`  
创建专有目录  
`install --owner=nextcloud --group=nextcloud --mode=700 -d /var/lib/nextcloud/sessions`

## 3.数据库

修改事务隔离级别  
`sudo nvim /etc/my.cnf.d/server.cnf`

```
[mysqlcloud.example.comd] 
transaction_isolation=READ-COMMITTED
```

启动CLI工具  
`mysql -u root -p`  
Nextcloud创建用户和数据库

```
CREATE USER 'nextcloud'@'localhost' IDENTIFIED BY 'xxxxxxxx';
CREATE DATABASE IF NOT EXISTS nextcloud CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
GRANT ALL PRIVILEGES on nextcloud.* to 'nextcloud'@'localhost';
FLUSH privileges;
```

设置Nextcloud的数据库架构

```
occ maintenance:install \
    --database=mysql \
    --database-name=nextcloud \
    --database-host=localhost:/run/mysqld/mysqld.sock \
    --database-user=nextcloud \
    --database-pass=xxxxxxxx \
    --admin-pass=zzzzzzzz \
    --admin-email=aaaa@bbbbb \
    --data-dir=/var/lib/nextcloud/data
```

## 4.应用服务器

创建一个php-fpm特定的副本(应确保此文件只能由root写入)  
`cp /etc/php/php.ini /etc/php/php-fpm.ini`  
配置php-fpm  
`sudo nvim /etc/php/php-fpm.ini`  
`;zend_extension=opcache`  
并把以下参数放在[opcache]下方

```
opcache.enable = 1
opcache.interned_strings_buffer = 8
opcache.max_accelerated_files = 10000
opcache.memory_consumption = 128
opcache.save_comments = 1
opcache.revalidate_freq = 1
```

创建池文件 [参考](https://gist.github.com/wolegis/0d9c83acd0c8bf83bcfb3983931bc364) (应确保此文件只能由root写入)  
`sudo nvim /etc/php/php-fpm.d/nextcloud.conf`  
设置php-fpm服务  
`sudo nvim /etc/systemd/system/php-fpm.service.d/override.conf`

```
[Service]
ExecStart=
ExecStart=/usr/bin/php-fpm --nodaemonize --fpm-config /etc/php/php-fpm.conf --php-ini /etc/php/php-fpm.ini
ReadWritePaths=/var/lib/nextcloud
ReadWritePaths=/etc/webapps/nextcloud/config
```

启用并启动php-fpm服务  
`sudo systemctl enable --now php-fpm.service`

## 5.网络服务器

[参考](https://docs.nextcloud.com/server/latest/admin_manual/installation/nginx.html)  
把文件复制到 /etc/nginx/sites-available /etc/nginx/sites-enabled    
根目录应该更改到根目录应更改为root /user/share/webapps/nextcloud;  
启用并启动nginx服务  
`sudo systemctl enable --now nginx.service`

## 6.客户端

`paru -S nextcloud-client`

## 也可以看看

[Nextcloud的ArchWiki](https://wiki.archlinux.org/title/Nextcloud)  
[Nextcloud的官方文档](https://docs.nextcloud.com/)  
[Nextcloud的管理员手册](https://docs.nextcloud.com/server/latest/admin_manual/)