---
title: "使用Hugo搭建个人博客并部署在GitHubPages"
date: 2022-07-16T08:00:00+08:00
tags: ["Linux","Hugo","GitHub Pages"]
draft: false
---

## 1.安装Hugo

`paru -S hugo`

## 2.创建新站点

初始化一个hugo项目  
`hugo new site 你的Github用户名.github.io`

## 3.添加主题

从该页面选择自己心仪的[主题](https://themes.gohugo.io/) 我当前使用的是LoveIt主题  
进入该目录 创建一个空的git存储库 并使该存储库成为站点目录的子模块

```
cd 你的Github用户名.github.io
git init
git clone https://github.com/dillonzq/LoveIt.git themes/LoveIt
```

将主题在配置文件 config.toml 启用   
`theme = "LoveIt"`

## 4.添加文章

创建一篇空文章  
`hugo new posts/test.md`  
另外 需要将生成在文章(/content/post/test.md)头部的draft=true修改为draft=false 否则并不会生成草稿页面  
[更多信息](https://gohugo.io/getting-started/usage/#draft-future-and-expired-content)

## 5.创建GitHub Pages项目并配置

创建一个新的GitHub项目 项目名称需要是 <你的Github用户名.github.io> 格式  
配置Pages项目 改成从 /docs 中的文件夹构建GitHubPages 目前只支持从根(root)或docs目录中的文件夹构建站点

## 6.生成静态页面并启动Hugo服务

生成静态页面之前需要修改 config.toml 文件中的 baseURL 配置 将其修改为个人站点  
`baseURL = "https://你的Github用户名.github.io"`  
启动服务实时预览并生成内容到docs目录  
`hugo server -d docs --disableFastRender`  
进入 http://localhost:1313/ 预览页面

## 7.上传Github Pages项目

```
git remote add origin git@github.com:你的Github用户名/你的Github用户名.github.io.git
git fetch origin
git checkout main
git add .
git commit -m "init github pages"
git push origin
```

## 也可以看看

[Hugo官方文档](https://gohugo.io/documentation/)  
[Markdown基本语法](https://www.markdownguide.org/basic-syntax/)  
[LoveIt主题](https://github.com/dillonzq/LoveIt)  
[LoveIt主题的博客](https://hugoloveit.com/)