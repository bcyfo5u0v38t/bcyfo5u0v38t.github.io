---
title: "IntelliJ系IDE使用fish Shell终端报无权限错误和无法创建目录解决办法"
date: 2022-07-21T08:00:00+08:00
tags: ["ArchLinux","fish shell","shell","IntelliJ","故障排除"]
categories: ["ArchLinux"]
draft: false
---

## 1.设置软链接

`sudo ln -s ~/.config/fish/completions /opt/intellij-idea-ultimate-edition/plugins/terminal/fish/completions`  
`sudo ln -s ~/.config/fish/conf.d /opt/intellij-idea-ultimate-edition/plugins/terminal/fish/conf.d`  
`sudo ln -s ~/.config/fish/functions /opt/intellij-idea-ultimate-edition/plugins/terminal/fish/functions`  
`sudo ln -s ~/.config/fish/fish_variables /opt/intellij-idea-ultimate-edition/plugins/terminal/fish/fish_variables`

## 也可以看看

[JetBrains的产品文档](https://www.jetbrains.com/help/)  
[fishshell的文档](https://fishshell.com/docs/current/index.html)