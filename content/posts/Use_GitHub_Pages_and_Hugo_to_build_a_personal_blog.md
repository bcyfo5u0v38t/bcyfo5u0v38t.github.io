---
title: "使用GitHub Pages和Hugo搭建个人博客(Windows)"
date: 2022-07-17T07:34:17+08:00
draft: false
---

## 1.安装Hugo

从Github [Releases](https://github.com/gohugoio/hugo/releases)页面下载 hugo_extended_xxx_Windows-64bit.zip  
将压缩包解压到用户目录(防止权限问题)下并添加到环境变量Path中  
![Path](/img/Use_GitHub_Pages_and_Hugo_to_build_a_personal_blog-Path.png)  
重启后 使用`hugo version`命令验证安装是否成功  
![hugo version](/img/Use_GitHub_Pages_and_Hugo_to_build_a_personal_blog-hugo_version.png)

## 2.创建新站点

初始化一个hugo项目  
`hugo new site yourgithubusername.github.io`

## 3.添加主题

从该页面选择自己心仪的[主题](https://themes.gohugo.io/) 我当前使用的是m10c主题
进入该目录 并下载m10c主题

```
cd yourgithubusername.github.io
git clone https://github.com/vaga/hugo-theme-m10c.git themes/m10c
```

将主题到站点配置(config.toml)中  
`theme = "m10c"`

## 4.添加文章

创建一篇空文章  
`hugo new post/test.md`  
另外 需要将生成在文章(/content/post/test.md)头部的draft=true修改为draft=false 否则并不会生成草稿页面
[更多信息](https://gohugo.io/getting-started/usage/#draft-future-and-expired-content)

## 5.创建GitHub Pages项目并配置

创建一个新的 GitHub 项目 项目名称需要是 <yourgithubusername.github.io> 格式
![创建一个项目](/img/Use_GitHub_Pages_and_Hugo_to_build_a_personal_blog-Create_a_new_repository.png)  
配置Pages项目  
![配置Pages项目](/img/Use_GitHub_Pages_and_Hugo_to_build_a_personal_blog-Pages.png)

## 6.生成静态页面并启动Hugo服务

生成静态页面之前需要修改 config.toml 文件中的 baseURL 配置 将其修改为个人站点
`baseURL = "https://yourgithubusername.github.io"`
GitHub Pages目前只支持从根(root)或docs目录中的文件夹构建站点
`hugo server -d docs`  
进入 http://localhost:1313/ 预览页面

## 7.上传Github Pages项目

```
git remote add origin git@github.com:yourgithubusername/yourgithubusername.github.io.git
git fetch origin
git checkout main
git add .
git commit -m "init github pages"
git push origin

```

## 也可以看看

[Hugo官方文档](https://gohugo.io/documentation/)
[Markdown基本语法](https://www.markdownguide.org/basic-syntax/)