---
title: Linux搭建饥荒云服务器
date: 2020-04-09 15:15:54
tags: [game]
categories: [game,饥荒]
comments: true
---
本文记录饥荒steam云服务器搭建过程，以及碰到的一些问题，后续遇到问题再补充。
<!--more-->
服务器：阿里云 Ubuntu 18.04 64位
linux系统资源占用比win系统少，卡顿也会更少。

### 安装环境依赖

```bash

sudo apt-get install libstdc++6:i386 libgcc1:i386 libcurl4-gnutls-dev:i386 lib32gcc1

```

- 安装失败可以尝试先运行`sudo apt-get update`命令，再安装

### 安装SteamCMD

- 新建`steamcmd`目录

此目录用于 steam 程序的安装目录。

```bash
mkdir ~/steamcmd
```

- 下载 SteamCMD 安装文件

```bash
wget -P ~/steamcmd https://steamcdn-a.akamaihd.net/client/installer/steamcmd_linux.tar.gz
```

- 解压压缩包

```bash
cd ~/steamcmd
tar -xvzf ~/steamcmd/steamcmd_linux.tar.gz
```

### 安装饥荒服务端

- 启动 steamcmd

```bash
./steamcmd.sh
```

请耐心等待客户端安装，直到进入了: `Steam>` 命令行模式。
如有遇到安装进度不走，请使用 `ctrl + c` 强制退出后重试。

- 使用公共账户登录 Steam

```bash
login anonymous
```

- 指定游戏文件的安装目录

```bash
force_install_dir ../dontstarvetogether_dedicated_server
```

- 安装饥荒服务端

```bash
app_update 343050 validate
```

安装完成后，使用以下命令退出 Steam 客户端。

```bash
quit
```

成功安装所出现的标志：
`Success! App '343050' fully installed.`

### 配置饥荒存档

#### 客户端创建世界(与正常创建相同)

进入游戏，选择 创建游戏 ， 联机版 ，风格随意，在进一步的界面中填写相应内容（名称、描述、密码等）；

切换选项卡至 森林 ，在此界面可以修改相关世界属性，同理 洞穴 选项卡配置的是地下部分的世界属性（先选择增加洞穴）；

切换到 模组 选项卡，勾选需要在服务端启动的模组（记得同时配置模组）。

大体的配置内容完成后，选择 形成世界 进入游戏，等到人物选择界面后即可退出客户端。

#### UserID

回到饥荒首页，点击位于右下角的 账号 按钮，进入浏览器，记录用户 ID 值。

#### cluster_token

在上一步的网页中，点击导航栏的 游戏 ，再点击 DST 的 服务器，进入页面后添加新服务器并记录内容。

#### 提取配置文件

进入目录`C:\Users\ZZ（这是电脑的名字）\Documents\Klei\DoNotStarveTogether\459953557`,在该目录的下一级目录中 有一个命名为 `Cluster_X` 的文件夹（X 为数字编号），提取出刚刚创建的存档。。
也可以在`我的文档`中找到`Klei`目录进入文件夹。
找到存档`Cluster_X`后复制到上一级目录作为备份。

- 以下均为修改备份文件

#### 配置令牌和服务器管理员

配置令牌：
在`Cluster_X`目录中找到`cluster_token.txt`文件（没有找到则创建），将之前复制的服务器Token值覆盖里面的内容。

配置管理员:
新建 `adminlist.txt` 文件，再将此前获取到的 `UserID` 复制到文件中。

```bash
touch adminlist.txt
```

#### 配置cluster.ini

打开文件`cluster.ini`，修改以下内容：

```bash
bind_ip = 0.0.0.0
master_ip = 你的云服公网IP
```

### 配置饥荒服务端

#### 新建存档目录

饥荒的配置目录位于: `~/.klei/DoNotStarveTogether`
该目录下存放的是游戏存档，我们手动创建一个存档目录并进入文件夹：

```bash
mkdir -p ~/.klei/DoNotStarveTogether/MyDediServer
cd ~/.klei/DoNotStarveTogether/MyDediServer
```

#### 上传存档

在电脑中打开cmd，输入以下命令上传存档

```bash
scp -r C:\Users\ZZ\Documents\Klei\DoNotStarveTogether\Cluster_1 root@云服公网IP:.klei/DoNotStarveTogether/MyDediServer/
```

win系统下需要输入完整的存档路径。

#### 配置存档目录

- 单击服务器上方的文件，打开新文件树。
- 在文件树中进入目录`root -> .klei -> DoNotStarveTogether -> MyDediServer`
- 将`Cluster_1`文件夹中`Caves`、`Master`、`adminlist.txt`、`cluster_token.txt`、`cluster.ini`拖动到目录`MyDediServer`中。

操作完毕后`Cluster_1`可删除。

配置完成后的目录如下：

![](https://raw.githubusercontent.com/sfershmiss11/Photo/master/20200414160933.png)

### mod配置

进入到服务器的的饥荒安装目录：

```bash
cd ~/dontstarvetogether_dedicated_server/mods
```

修改该目录下的 `dedicated_server_mods_setup.lua` 文件：

```bash
vim dedicated_server_mods_setup.lua
```

```bash
# 在该文件中添加类似这样的内容，这串 ID 是 Mod 文件在 Steam 中的 ID
# 可以在提取到的配置文件的 Master/modoverrides.lua 中找到
# 然后添加到本文件中，一行一个
ServerModSetup("362175979")
```

若在vim中无法输入请按a键，输入完毕后按ecs键后再按`:wq`保存退出。
mod序号在steam中右击游戏打开文件所在地，进入`mods`文件夹中查看。

### 启动游戏

- 启动游戏的脚本
回到home目录下新建脚本文件并添加内容：

```bash
cd ~ && touch startDST.sh
vim startDST.sh
```

内容如下：

```bash
steamcmd_dir="$HOME/steamcmd"
install_dir="$HOME/dontstarvetogether_dedicated_server"
cluster_name="MyDediServer"
dontstarve_dir="$HOME/.klei/DoNotStarveTogether"

function fail()
{
        echo Error: "$@" >&2
        exit 1
}

function check_for_file()
{
    if [ ! -e "$1" ]; then
            fail "Missing file: $1"
    fi
}

cd "$steamcmd_dir" || fail "Missing $steamcmd_dir directory!"

check_for_file "steamcmd.sh"
check_for_file "$dontstarve_dir/$cluster_name/cluster.ini"
check_for_file "$dontstarve_dir/$cluster_name/cluster_token.txt"
check_for_file "$dontstarve_dir/$cluster_name/Master/server.ini"
check_for_file "$dontstarve_dir/$cluster_name/Caves/server.ini"


check_for_file "$install_dir/bin"

cd "$install_dir/bin" || fail 

run_shared=(./dontstarve_dedicated_server_nullrenderer)
run_shared+=(-console)
run_shared+=(-cluster "$cluster_name")
run_shared+=(-monitor_parent_process $$)

"${run_shared[@]}" -shard Caves  | sed 's/^/Caves:  /' &
"${run_shared[@]}" -shard Master | sed 's/^/Master: /'
```

给启动脚本添加权限：

```bash
chmod u+x ./startDST.sh
```

- 启动游戏

利用 screen 命令启动一个新窗口，以便后台运行：

```bash
screen -S DST
```

运行失败请先下载screen命令:

```bash
sudo apt-get install screen
```

启动脚本，开始游戏：

```bash
./startDST.sh
```

### 关闭服务器

进入游戏中，在你创建的世界中的控制台输入`c_shutdown()`

- 第二次开服失败请检查之前服务器是否关闭

### 常见问题

#### 地下开启失败

地下连接地上失败请检查代码是否正确或者安全组端口是否开放

#### 更换存档

将新存档上传后把原存档覆盖

#### 服务器无法关闭

方法一：重启云服
方法二：服务器输入指令回到DST窗口：`screen -r DST`
        再输入`ctrl+c`结束服务器运行

#### 如何直接连接

控制台输入：`c_connect("云服公网IP",端口号,密码)`
端口号为目录`Cluster_1`->`Master`->`server.ini`里的server_port

#### 服务器版本低

服务器上的饥荒没有更新：
输入代码：

```bash
cd ~/steamcmd
./steamcmd.sh
login anonymous
force_install_dir ../dontstarvetogether_dedicated_server //改为自己的安装路径
app_update 343050 validate
quit
```

更新后仍然有问题，多更新几次;或检查路径是否正确。

#### 更改服务器使用mod

首先在客户端的任意存档中打开需要开启的服务器mod，并配置好后，
打开本地存档文件夹`Master`和`Caves`中分别有文件`modoverrides.lua`
打开`modoverrides.lua`文件，把不需要的mod部分删去后，上传服务器，覆盖地上地下里的原文件。

#### 部分mod无作用

使用部分改变地形的mod，如棱镜、暴食等，服务器开启成功后请重新生成世界。

### 参考文章

[Linux 服务器搭建饥荒服务端](https://blog.csdn.net/szhiy/article/details/79996017)
[饥荒联机版搭建 Ubuntu 专用服务器](https://www.dazhuanlan.com/2019/12/05/5de87a80aac19/)
