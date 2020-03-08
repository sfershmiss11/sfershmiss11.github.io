---
title: hexo部署到阿里云ECS
date: 2020-03-05 16:18:50
tags: [hexo,云服务]
categories: [hexo]
comments: true
---
一开始将博客搭建在GitHub上，由于加载速度太慢忍受不了，刚部署完不久就开始研究如何加快博客加载。尝试了将博客双线搭建在GitHub+coding上速度仍然不理想，找到不到其他办法便买了个ECS服务器搭建博客，同时学习Linux。
<!--more-->
 - 阿里云ECS	Ubuntu 18.04 64位
 - Git Hooks 

## 要求

 1. 域名（国内需备案）
 2. 云服务器ECS

## 购买域名
域名可以去国内几大云服务商购买，如：[万网](https://wanwang.aliyun.com/?spm=5176.12825654.eofdhaal5.77.54212c4aXMbw9Y)

## 购买ECS
购买完域名后，回到首页进入[ECS](https://www.aliyun.com/product/ecs?spm=5176.12825654.eofdhaal5.2.54212c4algJxM5)购买服务器。

## 备案域名
购买完服务器后才能给域名[备案](https://bsn.console.aliyun.com/?spm=5176.13329450.nav-right.dconsoleEntry.32f14df5REO2Rh#/?_k=cqg8w2)，按照提示完成备案等待3~5天左右，期间会有客服打电话核实。

## 安装服务器系统
选择Ubuntu 18.04 64位，不喜欢后面可以改。

## 安装nvm
nvm是nodejs的版本管理工具，可以快速切换更新nodejs版本。
之前在安装node.js时遇到直接安装的node.js版本过低报错，所以我们使用nvm安装。
curl下载：
```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash
```
wget下载：

```bash
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash
```
以上均报错的话，使用`yum`安装。

## 安装Node.js
执行：`nvm install nginx`输入 `nginx -V` 可以看到 nginx 版本信息，则安装成功。

## 安装 Git
执行：`yum install git`，完成后通过 `git --version` 查看 Git 版本，若显示版本信息则说明安装成功。

## 创建 git 用户
执行：adduser git，根据提示设置密码。

## 赋予 git 用户 sudo 权限

执行：

```bash
chmod 740 /etc/sudoers
vim /etc/sudoers
```
找到以下内容：

```bash
# User privilege specification
root    ALL=(ALL:ALL) ALL
```
在 `root ALL=(ALL:ALL) ALL` 这一行下面添加 `git ALL=(ALL:ALL) ALL`

保存退出后，修改回文件权限：

```bash
chmod 440 /etc/sudoers
```

## 关闭 git 用户 shell 权限

我们也可以通过：

```bash
ssh git@VPS IP
```

ssh 连接服务器，登录到服务器上，对服务器进行各种操作，这通常很不安全，也不合适，我们只需要能对仓库操作就可以了，不需要更大的权限。

因此我们关闭 git 用户 shell 权限，执行：

```bash
vim /etc/passwd
```
将最后一行的 `git:x:1001:1001:,,,:/home/git:/bin/bash` 修改为 `git:x:1001:1001:,,,:/home/git:/usr/bin/git-shell`

## 初始化 git 仓库

```bash
cd /home/git                //切换到git用户目录
mkdir blog.git              //创建git仓库文件夹，以blog.git为例
cd blog.git                 //进入仓库目录
git init --bare             //使用--bare参数初始化为裸仓库，这样创建的仓库不包含工作区
```
注意：裸仓库没有工作区，因为服务器上的 Git 仓库纯粹是为了共享，所以不让用户直接登录到服务器上去改工作区，并且服务器上的 Git 仓库通常都以.git 结尾。

## 创建网站目录

```bash
cd /var/www/                //切换目录
mkdir blog                  //创建网站目录，以blog为例
```

## 配置 SSH

```bash
cd /home/git                //切换到git用户目录
mkdir .ssh                  //创建.ssh目录
cd .ssh
vim authorized_keys
```
然后将本地的公钥复制到 authorized_keys 文件里 (公钥即本地执行 cat ~/.ssh/id_rsa.pub 查看的内容，若无法运行则需创建)。

注意：收集所有需要登录的用户的公钥，就是他们自己的 id_rsa.pub 文件，把所有公钥导入到 /home/git/.ssh/authorized_keys 文件里，一行一个。

## 用户组管理

ll /home/git/
ll /var/www/
确保 `blog.git`、`.ssh、blog` 目录的用户组权限为 `git:git`，若不是，执行下列命令：

```bash
chown -R git.git /home/git/blog.git/
chown -R git.git /home/git/.ssh/
chown -R git.git /var/www/blog/
```

## 配置 nginx

```bash
cd /etc/nginx/sites-available               //切换目录
cp default default.bak                  //备份默认配置
vim default                     //修改配置
```
参考配置改动:

```bash
root /var/www/blog;		//网站根目录
server_name guchcx.fun, www.guchcx.fun;  #网址
```
保存退出后，启动 nginx：

```bash
systemctl start nginx
```
设置开机自动启动：

```bash
systemctl enable nginx
```

查看运行状态：

```bash
systemctl status nginx
```
显示 running 表示成功运行。

## 配置 Git Hooks

创建 post-receive 文件

```bash
su git
```

git 用户下执行（这里我用 root 用户执行上述命令，然后更改了文件所有者为 git.git）：

```bash
cd /home/git/blog.git/hooks     //切换到hooks目录下
vim post-receive            //创建文件
```
复制下面的内容到 post-receive 文件中：

```bash
#!/bin/bash
echo "post-receive hook is running..."

GIT_REPO=/home/git/blog.git
TMP_GIT_CLONE=/tmp/blog
PUBLIC_WWW=/var/www/blog

rm -rf ${TMP_GIT_CLONE}
git clone $GIT_REPO $TMP_GIT_CLONE
rm -rf ${PUBLIC_WWW}/*
cp -rf ${TMP_GIT_CLONE}/* ${PUBLIC_WWW}
```
赋予可执行权限：

```bash
chmod +x post-receive
```

## 本地操作

尝试连接
在本地打开 Git Bash：

```bash
ssh git@VPS的ip
```
若默认端口不是 22，则需要在后面加上 -p 端口号：

```bash
ssh git@VPS的ip -p 2022
```

## 配置 Hexo

打开本地博客根目录下的_config.yml 文件，找到最后的 deploy 配置，修改为：

```bash
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
    type: git
    # repo: git@VPS IP:/~/blog.git  # 默认端口生效使用
    # repo: ssh://git@VPS IP:端口/~/blog.git # 默认端口不生效使用，设置端口
    branch: master
```

## 参考文章：
[Hexo 博客部署到 VPS](https://tding.top/archives/12a4e7e6.html)