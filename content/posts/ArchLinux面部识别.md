---
title: "ArchLinux面部识别"
date: 2022-08-15T08:00:00+08:00
tags: ["Linux","Howdy","Authentication"]
categories: ["Linux"]
draft: false
---

## 1.安装Howdy

`paru -S howdy`

## 2.配置

你想用howdy通过pam来认证 就在那个配置文件里加入下面这个配置

```
auth sufficient pam_unix.so try_first_pass likeauth nullok    #这行应当放在文件的顶部
auth sufficient pam_python.so /lib/security/howdy/pam.py
```

示例  
`sudo nvim /etc/pam.d/sudo`

```
#%PAM-1.0
auth sufficient pam_unix.so try_first_pass likeauth nullok
auth sufficient pam_python.so /lib/security/howdy/pam.py
auth            include         system-auth
account         include         system-auth
session         include         system-auth
```

## 3.添加正确的摄像头

安装v4l-utils  
`paru -S v4l-utils`  
查看设备  
`sudo v4l2-ctl --list-devices`  
添加摄像头路径  
`sudo nvim /lib/security/howdy/config.ini`  
`device_path = 摄像头设备路径`  
测试  
`sudo howdy test`

## 5.添加面容数据

`sudo howdy add`  
根据提示随便输入一个模型名称(不能超过24个字符)  
面向摄像头 让它认识你  
你会看到 Added a new model to 你的用户名 这就代表完成了  
列出所有已保存的人脸模型  
`sudo howdy list`

## 也可以看看

[Howdy的Github](https://github.com/boltgolt/howdy)  
[Howdy的ArchLinux](https://wiki.archlinux.org/title/Howdy)