title: Android cloud emulator
date: 2014-01-08 14:55:34
tags: [android Emulator]
categories: Android
---

最近闲来无事弄下Android，想做个APP练练手，以前一直有这想法，只是没有实现O^O,电脑的配置实在在捉急了，昨天晚上搭好环境后第一次启动模拟器大概花了20分钟，我都不记得漫长的等待过程中的那种感受了，今天google了下云端的模拟器，觉得很赞，因为健忘所以写篇blog记录下。

大致研究了两个Cloud Emulator： [manymo](https://www.manymo.com/)和[genymotion](http://www.genymotion.com/)

<!--more-->

1.先note genymotion,因为这个是成功了的。
Step1:[注册genymotion账号](http://www.genymotion.com/)

Step2:[下载Genymotion安装包](https://cloud.genymotion.com/page/launchpad/download/)

Step3:安装，按指示傻瓜式安装就好了。

成功之后，进入Genymotion，add,用Genymotion登录，选择一个device点击next就好。


Step4:在eclipse中安装genymotion插件。

(1).在线安装：启动Eclipse，Help->Install New Software...->Add,输入：Name: Genymobile   Location: http://plugins.genymotion.com/eclipse

(2).手动安装：[下载Eclipse插件](https://cloud.genymotion.com/page/launchpad/download/),将下载好的插件jar文件放到你eclipse安装目录下的plugins目录下，然后启动eclipse.

在eclipse中ctrl+6启动该插件，首次使用时要指定Genymotion安装目录和SDK目录

2.manymo：速度还不错，在浏览器中运行还比较流畅，但是不知道为什么电脑一直配置没成功，于是才转向了genymotion。下面只是是配置笔记~有成功的欢迎分享。
	
在Eclipse中配置ManyMo：

(1).[installation manymo program](https://www.manymo.com/ManymoSetup.exe)

(2).Authentication Token：Win+R→cmd→输入：manymo token

(3).Android SDK Platform Tools配置，下载[Android SDK](http://developer.android.com/sdk/index.html)后配置path,path:E:\android\adt-bundle-windows-x86-20131030\sdk\platform-tools

(4)模拟器本地化：

访问[manymo](https://www.manymo.com/)，点击develop，copy:manymo tunnel w3d.manymo.com:5556:jCYdPc3 ,在cmd中执行（不知道为什么我在这儿就报错了，一直没解决，然后我就转向了genymotion了）

关闭tunnel：manymo shutdown SERIALNUMBER 

查看正在运行的device:adb devices

(5).在eclipse中，在创建的android项目上，点run configuration→选择Target→ Deployment Target Selection Mode：Always prompt to pick device→close


Last:开始develop吧，虽然很多人说java不好-_-!

参考：

[Android模拟器Genymotion安装向导](http://blog.csdn.net/henren555/article/details/9700027)

[快到极致的Android模拟器——Genymotion](http://my.oschina.net/ryanhoo/blog/141824)





