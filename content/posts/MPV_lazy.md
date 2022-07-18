---
title: "MPV(视频播放器)_lazy懒人包 集成Anime4K...(Windows)"
date: 2022-07-18T07:10:18+08:00
draft: false
---

## 1.下载MPV_lazy

GitHub只有源码 从奶牛[下载](https://hooke007.cowtransfer.com/s/143a3d6e402540)  
下载下来是一个.exe文件  
重命名为.7z文件并解压  
移至用户目录下(防止权限问题) 路径不要有中文  
允许Windows执行PowerShell脚本 以管理员身份打开PowerShell输入
`set-executionpolicy remotesigned`
打开目录下的installer文件夹 右键以管理员权限运行mpv-install.bat  
到这一步就可以使用了

## 2.安装Play-With-MPV在线播放插件

安装Chrome浏览器插件[Tampermonkey](https://chrome.google.com/webstore/detail/tampermonkey/dhdgffkkebhmkfjojejmpbldmpobfkfo?hl=zh-CN)  
安装油猴脚本[Play-With-MPV](https://greasyfork.org/zh-CN/scripts/444056-play-with-mpv)  
从GitHub Releases下载[Play-With-MPV](https://github.com/LuckyPuppy514/Play-With-MPV/releases)(Source code (zip))  
解压到MPV目录下并重命名为Play-With-MPV(参考C:\Users\bcyfo5u0v38t\Software\MPV\Play-With-MPV)  
进入Play-With-MPV目录下 右键使用PowerShell运行install.ps1

## 3.添加着色器运行模式缓存插件

从GitHub
Releases下载[MPV_Glsl_Running_Mode_Cache](https://github.com/LuckyPuppy514/MPV_Glsl_Running_Mode_Cache/releases/tag/v1.0.0)  
解压 复制里面的lua脚本到MPV\portable_config\scripts目录下  
打开input.conf文件 添加以下内容 并注释(前面加#)掉原来的配置

```
## 着色器模式 GLSL MODEL: Glsl_Running_Mode_Cache.lua
 CTRL+1 script-binding Glsl_Model_1
 CTRL+2 script-binding Glsl_Model_2
 CTRL+3 script-binding Glsl_Model_3
 CTRL+4 script-binding Glsl_Model_4
 CTRL+5 script-binding Glsl_Model_5
 CTRL+6 script-binding Glsl_Model_6
 CTRL+7 script-binding Glsl_Model_7
 CTRL+8 script-binding Glsl_Model_8
 CTRL+9 script-binding Glsl_Model_9
 CTRL+0 script-binding Glsl_Model_0
```

## 4.添加复制粘贴网址播放插件

从GitHub Releases下载[mpv-scripts](https://github.com/Eisa01/mpv-scripts/releases)(Source code (zip))  
解压 复制里面的script-opts和scripts文件夹到MPV\portable_config目录下

##也可以看看
[懒人包快速说明](https://hooke007.github.io/mpv-lazy/%5B00%5D_%E6%87%92%E4%BA%BA%E5%8C%85%E5%BF%AB%E9%80%9F%E8%AF%B4%E6%98%8E.html)  
[懒人包作者博客](https://hooke007.github.io/index2#index2-of-hooke007)  
[Windows MPV教程](https://hooke007.github.io/unofficial/mpv_start.html)  
[其他MPV中文教程](https://github.com/hooke007/mpv_doc-CN)  
[简易包mpv.net_CM](https://github.com/hooke007/mpv.net_CM)  