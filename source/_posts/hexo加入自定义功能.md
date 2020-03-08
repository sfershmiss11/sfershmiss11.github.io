---
title: hexo加入自定义功能
date: 2020-03-03 12:07:12
tags: [hexo]
categories: [hexo,next]
comments: true
---
给博客加入豆瓣读书、电影，音乐播放器功能。

<!--more-->
## Hexo 加入豆瓣读书页面
在 Hexo 博客个性化定制中，加入豆瓣读书界面是一个很不错的功能。GitHub 上已经有人写好了这个模块：hexo-douban，我们只需要安装模块，然后配置一下就可以了。

### **安装**

```bash
npm install hexo-douban --save
```

### **配置**
将下面的配置写入站点的配置文件_config.yml 里

```bash
douban:
  user: mythsman
  builtin: false
  book:
    title: 'This is my book title'
    quote: 'This is my book quote'
  movie:
    title: 'This is my movie title'
    quote: 'This is my movie quote'
  game:
    title: 'This is my game title'
    quote: 'This is my game quote'
  timeout: 10000
```

user: 你的豆瓣 ID. 打开豆瓣，登入账户，然后在右上角点击 “个人主页” ，这时候地址栏的 URL 大概是这样：”https://www.douban.com/people/xxxxxx/“，其中的”xxxxxx” 就是你的个人 ID 了。
builtin: 是否将生成页面的功能嵌入 hexo s 和 hexo g 中，默认是 false, 另一可选项为 true (1.x.x 版本新增配置项)。
title: 该页面的标题。
quote: 写在页面开头的一段话，支持 html 语法。
timeout: 爬取数据的超时时间，默认是 10000ms , 如果在使用时发现报了超时的错 (ETIMEOUT) 可以把这个数据设置的大一点。
如果只想显示某一个页面 (比如 movie)，那就把其他的配置项注释掉即可。

### **启动**
我们只需要在 Git Bash 中输入以下命令：hexo clean && hexo douban -bgm && hexo g && hexo s 即可，注意其中开启 hexo-douban 的命令中，-bgm 代表的是 book、game、movie 三个参数，如果只需要其中的一部分就只带你想要的那些参数。

**注意**：由于 hexo douban 的简写也是 hexo d，与 hexo deploy 的简写指令 hexo d 冲突，因此在进行二者部署的时候，只能都打全名而不能打简写形式。

 - 在 0.x.x 版本中，文章的更新和豆瓣页面的爬取操作是绑定在一起的，无法支持单独更新文章或者单独爬取文章。 
 - 在 1.x.x 版本中，使用
   hexo douban 命令即可生成指定页面，如果不加参数，那么默认参数为 - bgm。如果配置了 builtin 参数为
   true，那么除了可以使用 hexo douban 命令之外，hexo g 或 hexo s 也内嵌了生成页面的功能。因此，还是按照
   hexo clean && hexo g && hexo s 就可以。

### **测试**
上面都没问题之后，我们只需要在站点目录下测试 http://localhost:4000/books 或者 http://localhost:4000/movies 等，如果看到页面了就说明成功了。

### **部署**
如果上述都没有问题，我们就可以在菜单栏中添加按钮了，打开主题配置文件_config.yml，找到菜单按钮，添加下面内容：

```bash
menu:
  movies: /movies/ || film
  books: /books/ || book
```

然后在 next/language/zh-CN.yml 文件中添加对应的中文参数信息：

```bash
menu:
  movies: 观影
  books: 阅读
```

至此，我们就完成了豆瓣读书页面的添加。

## 实现 hexo 相册
准备工作
　　* 本节内容是基于实现本地保存图片功能前提记录的笔记，如果是用图床外链，可以跳过所有涉及到img文件夹以及img/s文件夹的步骤。
　　* 本相册的所有功能均仅测试了在hexo-theme-next下实现的效果，使用其它主题时部分布局尺寸数据可能会出现偏差，需要自行根据主题设置修改。

###  创建目录
在 git bash 中输入命令hexo new page gallery；
在hexo/sourse/gallery目录下建立你需要的分类相册文件夹；
进入hexo/sourse/gallery目录，新建img文件夹用来存放相册封面；
在每个相册文件夹中创建img文件夹用来存放大图，以及img/s文件夹用来存放缩略图。
Ps. img/s文件夹可以省略不建，用文件名-s.jpg的格式重命名缩略图即可。

### 图片处理
原图处理：
将需要上传的图片放入gallery/相册名/img文件夹中，为了加载速度建议压缩至 1mb 以下，使用 jpg 格式保存；
除了文件大小以外没有其它要求，但是为了方便后续页面编辑，建议将文件以容易辨识的方式重命名，例如日期-图片名.jpg。
缩略图处理：
将gallery/相册名/img下的所有图片复制到gallery/相册名/img/s文件夹中，使用 PS 等其它处理工具压缩图片大小；
建议宽度为 300px，最终输出文件大小在 30kb 左右，高度无要求。
封面图处理
从每个相册的缩略图中挑选需要的封面，复制到gallery/img文件夹中；
建议重命名为相册名.jpg。
### 相册主界面
打开gallery/index.md，将title设置成你需要的相册页面名称；
（可选）在日期下方加上comments: false关闭评论；
复制以下代码粘贴至正文，并按需求修改相册描述、相册名、相册文件夹名以及封面图文件名：

```bash
<center>！相册描述【此行可删除】</center>
<center>自定义分隔符【此行可删除】</center>
<div class="gallery-page">
	<div class="gallery-list">
		<div class="gallery-column">
			<div class="gallery-item">
				<a href="【！相册文件夹名】"><img src="img/【！封面图文件名】.jpg">
				</a>
				<p>- ！相册1 -</p>
			</div>
			<div class="gallery-item">
				<a href="【！相册文件夹名】"><img src="img/【！封面图文件名】.jpg">
				</a>
				<p>- ！相册2 -</p>
			</div>
		</div>
		<div class="gallery-column">
			<div class="gallery-item">
				<a href="sample"><img src="img/sample.jpg">
				</a>
				<p>- 相册名 -</p>
			</div>
		</div>
		<div class="gallery-column">
			<div class="gallery-item">
				<a href="sample"><img src="img/sample.jpg">
				</a>
				<p>- 相册名 -</p>
			</div>
			</div>
		</div>
	</div>
</div>
<center>自定义分割线【此行可删除】</center>
```

Ps. 1. 需要使用外链的场合，将< img src="img/【封面图文件名】.jpg">中的内容替换为图床外链地址即可；
Ps. 2. 代码中<div class="gallery-column">元素为分列显示相册的列数，可按需要增减；
Ps. 3. 新增相册时请确认代码添加在<div class="gallery-column">元素内部，否则会造成显示错误。

### 分类相册界面
打开gallery/相册名/index.md，将title设置成你需要的相册页面名称；
（可选）在日期下方加上comments: false关闭评论；
复制以下代码粘贴至正文，并按需求修改相册描述、图片名以及缩略图文件名：

```bash
<center>！相册描述【此行可删除】</center>
<center>自定义分隔符【此行可删除】</center>
<div class="gallery-page">
	<div class="img-list">
		<div class="img-column">
			<a href="img/【！图片名1】.jpg" target="_Blank"><img src="img/s/【！缩略图文件名1】.jpg"></a>
			<a href="img/【！图片名2】.jpg" target="_Blank"><img src="img/s/【！缩略图文件名2】.jpg"></a>
		</div>
		<div class="img-column">
			<a href="img/sample.jpg" target="_Blank"><img src="img/s/sample.jpg"></a>
		</div>
		<div class="img-column">
			<a href="img/sample.jpg" target="_Blank"><img src="img/s/sample.jpg"></a>
	</div>
</div>
<center>自定义分割线【此行可删除】</center>
```

Ps. 1. 需要使用外链的场合，将< img src="img/【图片名】.jpg">中的内容替换为图床外链地址即可，如果图床加载速度够快可以用同一个链接填充缩略图部分；
Ps. 2. 代码中< div class="img-column">元素为分列显示图片的列数，可按需要增减；
Ps. 3. 添加图片时请确认代码添加在< div class="img-column">元素内部，否则会造成显示错误。

### CSS 样式
在custom.styl中加入如下代码：

```bash
/*gallery*/

.gallery-page {
	margin-top: -50px;
}
.img-list,
.gallery-list {
	display: flex;
	flex-direction: row;
	flex-wrap: nowrap;
	align-items: flex-start;
}
.img-column {
	display: flex;
	flex-direction: column-reverse;
}
.img-column a,
.gallery-column a {
	border-bottom: 0px;
}
.gallery-item {
	margin-bottom: -50px
}
.gallery-item p {
	margin: -25px auto -10px;
	max-width: 50%;
	text-align: center;
	font-size: 15px;
	color: $black-deep;
	background: rgba(255,255,255,.3);
	border-radius: 7px;
	border: 1px solid $black-deep;
	box-shadow: 0 8px 20px -8px rgba(0,0,0,.3);
}
.posts-expand .post-body .gallery-column a img {
	height: 250px;
	width: 300px;
	object-fit: cover;
}
@media (max-width: 767px){
	.gallery-item p {
		min-width: 75px;
		font-size: 13px;
	}
}
```

Ps. 1. @media标签内的样式是防止移动端浏览时相册名被强制换行的，建议保留；
Ps. 2. 其余样式除了 flex 相关行与object-fit样式以外，均可根据需要自行更改，在此不作赘述。

无_custom/custom.styl 文件时，在根目录source下创建 _data文件夹创建styles.styl文件加入css代码，主题_config.yml 文件修改：

```bash
custom_file_path:
  #head: source/_data/head.swig
  #header: source/_data/header.swig
  #sidebar: source/_data/sidebar.swig
  #postMeta: source/_data/post-meta.swig
  #postBodyEnd: source/_data/post-body-end.swig
  #footer: source/_data/footer.swig
  #bodyEnd: source/_data/body-end.swig
  #variable: source/_data/variables.styl
  #mixin: source/_data/mixins.styl
  style: source/_data/styles.styl

```

## 加入音乐播放器
### **下载**
首先，先下载Aplayer源码：APlayer
### **dist文件夹**
再将文件中的dist文件夹复制到路径：blog/themes/next/source（我的next版本是7.5）
### **新建music.js**
在目录blog/themes/next/source/dist下添加music.js文件
内容是：

```bash
const ap = new APlayer({
    container: document.getElementById('aplayer'),
    fixed: true,
    autoplay: true,
    audio: [
	  {
        name: '勇气',
        artist: '棉子',
        url: 'http://music.163.com/song/media/outer/url?id=1411358329.mp3',
        cover: 'https://raw.githubusercontent.com/sfershmiss11/Photo/master/wife.png',
      },
      {
        name: '这就是爱吗',
        artist: '十豆',
        url: 'http://music.163.com/song/media/outer/url?id=1412242872.mp3',
        cover: 'https://raw.githubusercontent.com/sfershmiss11/Photo/master/wife.png',
      },

	  {
        name: '彼女は旅に出る',
        artist: '鎖那',
        url: 'http://music.163.com/song/media/outer/url?id=509106775.mp3',
        cover: '/images/1.jpg',
      },
	  {
        name: '失眠飞行',
        artist: '接个吻，开一枪 / 沈以诚 / 薛明媛',
        url: 'http://music.163.com/song/media/outer/url?id=1365898499.mp3',
        cover: '/images/2.jpg',
      },
	  {
        name: '冬眠',
        artist: '司南',
        url: 'http://music.163.com/song/media/outer/url?id=1398663411.mp3',
        cover: '/images/3.jpg',
      }
    ]
});
```
注：里面的代码可以修改，歌曲信息需要手动添加：

name：歌曲名称
artist：作者
url：连接（具体怎样操作看下面步骤）
cover：是图片连接，可以是本地图片，也可以是在线图片，本地放在source/images然后修改路径即可

播放器在跳转页面时音乐会重新播放，建议把autoplay设置为false。

### **url添加歌曲方法**
网易云网页播放一首歌曲
将网址中的id复制例：https://music.163.com/#/song?id=1411358329
将下面代码中的id修改为你想添加歌曲的id：http://music.163.com/song/media/outer/url?id=.mp3
### **修改文件_layout.swig**
在路径：blog/themes/next/layout下找到_layout.swig文件，然后将下面的代码添加到：<body itemscope ...>在body里面就可以

```bash
<link rel="stylesheet" href="/dist/APlayer.min.css">
<div id="aplayer"></div>
<script type="text/javascript" src="/dist/APlayer.min.js"></script>
<script type="text/javascript" src="/dist/music.js"></script>

```


参考文章：
[css+markdown 实现 hexo 相册【进阶篇】](https://co5.me/2018/181112-gallerry2.html)

[在Hexo博客中加入豆瓣读书页面](https://bestzuo.cn/posts/hexo-douban.html)

[Hexo next主题中添加播放器Aplayer](https://blog.csdn.net/qq_35324057/article/details/104124723)