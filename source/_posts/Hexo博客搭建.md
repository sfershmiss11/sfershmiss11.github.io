---
title: Hexo+Next博客搭建
date: 2020-03-02 09:48:33
tags: [hexo]
categories: [hexo,next]
comments: true
---
Hexo是一个快速，简单且功能强大的博客框架。您使用Markdown（或其他标记语言）编写帖子，然后Hexo会在几秒钟内生成带有精美主题的静态文件。
<!--more-->
要求：
 - node.js
 - git

### 1.创建GitHub仓库
登录github创建一个仓库，仓库名为：用户名.github.io
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200302101003818.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNTkwNzc4,size_16,color_FFFFFF,t_70)
### 2.Node.js
网址: [nodejs](https://nodejs.org/en/)
下载后自行安装，直接next到最后。
### 3.安装淘宝镜像
打开git bash	，输入命令：
`npm install -g cnpm --registry=https://registry.npm.taobao.org`
安装后输入cnpm -v可查看版本。
之后npm需改成cnpm。
### 4.安装hexo
输入命令安装：

```bash
cnpm install -g hexo-cli
```

### 5.创建blog文件夹

```bash
mkdir blog
```

### 6.初始化文件夹
输入命令：
```bash
cd blog
hexo init
```
cd为进入blog文件夹
### 7.安装上传工具
安装工具后才可hexo d 到仓库 ，安装命令：

```bash
cnpm install --save hexo-deployer-git
```
### 8.	下载主题
下载next主题在themes文件夹下的next里，命令：

```bash
git clone https://github.com/theme-next/hexo-theme-next themes/next
```

### 9.更改主题
打开根目录下的_config.yml文件，更改主题theme: landscape 改为 theme: next  ：


```bash
# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: next
```
同时修改文件：
```bash
deploy:
  type: git
  repository: https://github.com/sfershmiss11/sfershmiss11.github.io.git
  branch: master
```
repository为仓库地址。
### 10.创建新文章

```bash
hexo n 名字
```

### 11.编辑文章
文件夹中打开hexo\source\_posts下的.md文件写入文章内容，可用vscode、记事本或者其他的编辑器编辑。
文章内容要用Markdown语法编写。

### 12.配置blog基本信息
打开根目录下_config.yml文件：
```bash
# Site
title:          # 网站标题
subtitle:       # 网站副标题
description:    # 描述，介绍网站的
keywords:       # 网站的关键字
author:         # 博主姓名
language: zh-CN # 语言：zh-CN 是简体中文
timezone: UTC   # 时区
```
### 13.配置blog主页菜单
打开主题_config.yml文件

```bash
# 菜单设置为 菜单名: /菜单目录 || 菜单图标名字
menu:
  home: / || home
  about: /about/ || user
  tags: /tags/ || tags
  categories: /categories/ || th
  archives: /archives/ || archive
  #schedule: /schedule/ || calendar
  sitemap: /sitemap.xml || sitemap
  commonweal: /404/ || heartbeat

menu_settings:
  icons: true   # 显示图标
  badges: true  # 显示统计信息
```
### 14.添加新页面
git bash输入命令：
```bash
hexo n page Name
```

创建新页面名：about	，tags	，categories
### 15.next.yml
我们可以将所有主题配置放在一个位置（hexo/source/_data/next.yml）。这样就无需编辑主题配置文件（next/_config.yml）。
具体步骤：
在 hexo/source/_data 目录中创建 next.yml（如果_data 不存在，则创建目录）。
在 next.yml 设置 override 选项为 true。
将所有 NexT 主题选项从主题配置文件复制到 hexo/source/_data/next.yml 中。

### 16.主题样式
NexT 一共提供了 4 种首页样式，按照自己喜好选择一个，选择一个其他主题样式后其他的主题前一定要加上注释#：

```bash
# Schemes
#scheme: Muse
#scheme: Mist
#scheme: Pisces
scheme: Gemini
```
### 17.本地预览blog
输入命令：

```bash
hexo g
```

```bash
hexo s
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200302111946294.png)
然后在浏览器输入网址：localhost:4000 预览博客页面，git bash中输入ctrl+c退出预览.

### 18.上传到仓库
设置完毕后输入命令上传到github仓库：

```bash
hexo d
```
上传成功后浏览器输入：用户名.github.io 查看blog。