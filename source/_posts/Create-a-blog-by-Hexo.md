title: Build a blog by Hexo && Node.js
date: 2014-01-07 15:37:42
tags: [hexo,node.js]
categories: Hexo
---

##用hexo+node.js搭建blog

很久都没有学新东西了，三天前自己会的东西还是大学本科毕业后会的东西，在[@beforeload](http://beforeload.github.io/)的指导下搭建了算是属于自己的blog,算是更新了一下自己的知识库了吧。跟什么样的人在一起就会成为什么样的人，跟优秀的人在一起无可厚非的收获匪浅~技术更新真的很快，感觉自己目前在技术这条路上渐行渐远的节奏可能决定了以后都无法回头了，先暂且不想这些吧！踏踏实实过好每天就对了~这篇blog纯当练手用，顺便总结下自己作为新手搭的这个blog的大致步骤（虽然[hexo系列教程](http://zipperary.com/categories/hexo/)已经很多了）以及熟悉下[markdown语法](http://wowubuntu.com/markdown/)。       
<!-- more -->

**Step1: [install git](http://code.google.com/p/msysgit/)**

**Step2: [install Node.js](http://nodejs.org/)**

**Step3: [Hexo](http://zespia.tw/hexo/)**

- Install:   $ npm install hexo -g 
- SetUp:     $ hexo init blog && cd blog
- Generate:  $ hexo generate     
- Server:    $ hexo server

这时你可以 visit <http://localhost:4000>.看看效果了。

**Step4: **

登录[github](https://github.com/),new repositories，建立一个新的repository，repository的名字账号名对应，账号名.github.io,如mikiyonney.github.io              
生成SSH Keys，参考[SSH Generate](https://help.github.com/articles/generating-ssh-keys)

**Step5: 编辑E:\blog目录下_config.yml文件：**
```
deploy:
type: github
repository: git@github.com:MiKiYonney/mikiyonney.github.io.git
branch: master
```
修改完本地文件后，用hexo generate 生成与保存（Hexo命令在E:\blog目录下执行）,然后hexo deploy部署，这样如果一路没出错的话，你就可以happy的直接访问mikiyonney.github.io看你的blog了。
	
**Step6: 学习MarkDown，写第一篇blog**

我大致是参考的学习文档如下：

[MarkDown指南](http://zipperary.com/2013/05/22/introduction-to-markdown/)

[MarkDown官方文档](http://wowubuntu.com/markdown/)

用MarkDown可视化编辑工具能事半功倍，如Windows下的[MarkDownPad](http://markdownpad.com/)等等

然后 $ hexo new "New Post"，你就可以write whatever you want to write了。

**其他相关命令笔记**

- remove: $ rm -rf .deploy

- $ hexo clean  --Cleans the cache file (db.json) and generated files (public).

- Deploy

```
hexo generate --watch
hexo deploy --generate
```

- hexo alias：

```
hexo g == hexo generate
hexo d == hexo deploy
hexo s == hexo server
hexo n == hexo new
```

**其他参考**

- [使用hexo搭建博客](http://yangjian.me/workspace/building-blog-with-hexo/)

- [hexo教程](http://sys.la/2013/09/07/hexo/)

- [hexo blog](http://jiabin.tk/2013/06/21/using-hexo-as-blog-platform/)


---



