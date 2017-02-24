---
title: 'github博客诞生记'
date: 2017-02-13 09:45:17
tags:
categories: 技术
---

经历了年底的一波加班，年前总算有点时间可以学习下新知识了。首要任务就是把github的博客搭建起来，把一些学习心得梳理之后记录在这里。

一直以为github的博客是类似于博客园一样，有专门一个地方可以写博客的。其实不然，你要先建立一个仓库用来放github pages。这个就不赘述了，网上教程也很多。

至于如何生成github pages，jekyll推荐的比较多，我也去尝试了一下，需要安装ruby环境，编译本地调试非常不便。于是转为基于node环境的[hexo](https://hexo.io/zh-cn/)。本博客就是基于hexo搭建起来的。

#### 环境准备
1. 安装 [nodejs](http://nodejs.cn/)
2. 安装 [git](https://git-scm.com/)
3. 准备github仓库
注册了github账号之后，创建一个新的仓库。访问https://github.com/new，建立一个基于你的用户名的repository。以我为例，就是akizmh.github.io。新版本的github默认会把这个repo认为是你的github pages。
**注意：**创建github pages仓库的时候最好先去github上看看有无同名的。我一开始创建的是zmh.github.io，结果博客创建代码也提交了，但是页面就是显示不正确，后来才发现，github上已经有同名项目了，我访问的是别人的github pages。

<!-- more -->

#### hexo搭建blog
环境准备就绪之后，就可以开始创建博客了，只需要以下六步。
```
$ npm install hexo-cli -g
```
安装hexo命令行工具。
```
$ npm hexo init blog
```
生成hexo项目到blog文件夹。
```
$ cd blog
```
进入blog文件夹。
```
$ npm install
```
安装hexo项目所需的依赖。
```
$ hexo generate
```
编译生成最终博客的页面。
```
$ hexo server
```
启动hexo服务器，本地查看生成的博客。
打开浏览器，输入http://localhost:4000/，就可以本地访问我们的博客了。

我们来看一下hexo项目的目录。
```
.
├── _config.yml
├── package.json
├── scaffolds
├── source
|   ├── _drafts
|   └── _posts
└── themes
```
**_config.yml：**是hexo的配置文件。各配置参数在作用见[官方文档](https://hexo.io/zh-cn/docs/configuration.html)。*(注意配置文件里的key后面的冒号跟value之间一定要有一个空格)*
**scaffolds：**模版 文件夹。当您新建文章时，Hexo 会根据 scaffold 来建立文件。
**source：**资源文件夹是存放用户资源的地方。存放博客日志和草稿的markdown文件。
**themes：**主题 文件夹。Hexo 会根据主题来生成静态页面。
具体见https://hexo.io/zh-cn/docs/setup.html。

#### 更换主题
默认的博客主题有点原始，你可以去[hexo主题](https://hexo.io/themes/)挑选自己喜欢的主题。
选定自己喜欢的主题点进去之后，一般就是该应用该主题的作者的博客。找到作者的github地址，一般都会有比较详细的使用方法。
这里我们以比较热门的NexT这个主题为例，来看看如何换主题。
##### 1. 下载主题
进入我们的hexo项目目录，然后用git复制NexT主题代码。
```
$ cd your-hexo-site
$ git clone https://github.com/iissnan/hexo-theme-next themes/next
```
在/blog/themes/里，已经有一个landscape文件夹了，没错，这个就是hexo默认的主题。以后要使用什么主题，只要丢到这个文件夹里就OK了。

##### 2. 配置主题
打开hexo项目的配置文件_config.yml。
找到theme一项，把之前的主题landscape改成我们的新主题名NexT。
打开命令行，清楚之前主题生成的博客文件，重新生成，启动服务，完成。
```
$ hexo clean
$ hexo generate
$ hexo server
```
NexT是一个功能非常完善的主题，具体参见[NexT官网](http://theme-next.iissnan.com/)。其他主题的github仓库一般也给出了主题使用的方法。

#### 写博客
在命令行输入以下命令。
```
$ hexo new <title> 
```
title是要创建日志的标题。当然你也可以在对应markdown文件的[Front-matter](https://hexo.io/zh-cn/docs/front-matter.html)里修改标题名。
我们可以在source/_post里看到我们创建的新日志markdown文件。接下来就可以开始痛快的写日志啦。

#### 部署博客
hexo的部署方式有很多，这里我们选用git部署方式。
1. 首先在 _config.yml 中修改参数。
```
deploy:
  type: git
  repo: <repository url>
  branch: [branch]
  message: [message]
```
message是你每次部署的commit信息。不填默认为 Site updated: {{'YYYY-MM-DD HH:mm:ss'}}。
2. 然后安装部署工具
```
$ npm install hexo-deployer-git --save
```
3. 部署
```
$ hexo deploy
```
现在打开你的github pages。可以看到我们的新博客了。

#### 最后
本篇教程只是比较简单的走了一遍hexo搭建博客的流程。hexo本身就是非常不错的博客生成工具，更多功能还请参考[hexo官网](https://hexo.io/zh-cn/)。

本篇博客没有采用别人的主题，本着学习的目的，自己创建了一个新的主题，名为sanhua(三花)。配色采用的《夏目友人帐》猫咪老师身上的三种颜色，橙、灰和白。css预处理用的stylus，模板引擎用的是ejs。基本的功能都实现了。然而终究交互设计功力不足，做出来的效果差强人意，跟别人的高大上的主题想去甚远。自己先用着吧，后面不断完善。如果真有人喜欢这个主题，可以去我的github上下载：https://github.com/akizmh/hexo-theme-sanhua。

~Fin

