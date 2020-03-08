---
title: hexo备份到GitHub上
date: 2020-03-05 16:15:55
tags: [hexo,Github]
categories: [hexo]
comments: true
---
在搭建博客的过程中，由于很多攻略过时，版本已经更新，导致博客出现错误，然后我又做了很多操作后再查看，博客出现错误后无法还原。便重建了好几次博客，所以备份、备份、再备份。
<!--more-->
## 创建分支
在GitHub存放hexo网页的仓库创建一个分支hexo，回车键即可创建。
![](https://img-blog.csdnimg.cn/20200304215054853.png)

在设置中将hexo设置为默认分支
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020030421522257.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNTkwNzc4,size_16,color_FFFFFF,t_70)
在git bash上运行`git clone git@github.com:sfershmiss11/sfershmiss11.github.io.git`拷贝仓库。
拷贝后的仓库放哪都行。

## 备份

 1. 进入文件夹`sfershmiss11.github.io`若文件夹里有文件可全部删除
 2. 将原blog文件夹中的文件`_config.yml`，`themes/`，`source/`，`scaffolds/`，`package.json`，`.gitignore`复制至sfershmiss11.github.io文件夹。
 3. 将themes/next/(我用的是NexT主题)中的.git/删除，否则无法将主题文件夹push；
 4. 在sfershmiss11.github.io文件夹执行npm install和npm install hexo-deployer-git（这里可以看一看分支是不是显示为hexo）；
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200304220228762.png)
 5. 执行`git add .`、`git commit -m "..."`、`git push origin hexo`来提交hexo网站源文件；
 6. 然后回到原blog文件夹执行`hexo g -d`生成静态网页部署至Github上。
 
master分支和hexo分支，分别保存静态网页和源文件。
## 修改
在本地对博客修改（包括修改主题样式、发布新文章等）后：

 1. 将文件`_config.yml`，`themes/`，`source/`，`scaffolds/`，`package.json`，`.gitignore`复制到备份仓库覆盖
 2. 进入备份仓库依次执行`git add .`、`git commit -m "..."`、`git push origin hexo`来提交hexo网站源文件；
 3. 回到原blog文件夹执行`hexo g -d`生成静态网页部署至Github上。

## 恢复
重装电脑后，或者在其它电脑上想修改博客：

 1. 安装git；
 2. 安装Nodejs和npm；
 3. 使用`git clone git@github.com:sfershmiss11/sfershmiss11.github.io.git`将仓库拷贝至本地；
 4. 在文件夹内执行以下命令`npm install hexo-cli -g`、`npm install`、n`pm install hexo-deployer-git`。

## 参考文章
[hexo备份高赞回答](https://www.zhihu.com/question/21193762)
[Hexo博客备份](https://www.jianshu.com/p/57b5a384f234)