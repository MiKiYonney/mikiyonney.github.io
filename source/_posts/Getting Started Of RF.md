title: Robotframework配置与入门
date: 2014-8-22 11:43:40
tags: [Automated Testing]
categories: Testing
---

Robot Framework是一个基于关键字驱动的自动化测试框架。测试用例位于HTML或者TSV(以tab分隔值)文件，使用在测试库中实现的关键词来在测试中运行程序。因为Robot Framework 是灵活和可扩展的，所以它很合适用于测试具有多种接口的复杂软件：用户接口，命令行，web service，编程接口等。
<!-- more -->
1. 安装Python&&配置环境变量 
	- [下载Python](https://www.python.org/downloads/),请选择适合的Python版本！!
	- 环境变量配置：
	将D:\Python27 D:\Python27\Scripts 添加到系统环境变量。
	CMD中输入python，返回python版本则表示python环境安装成功。

2. Python快捷安装工具
	- [easy-install](http://peak.telecommunity.com/DevCenter/EasyInstall):下载ez_setup.py后，cmd窗口→python ez_setup.py即可。
	- [pip](https://pip.pypa.io/en/latest/installing.html):下载后py文件后，python get-pip.py或者直接easy_install pip即可。

3. 安装[RobotFramework](https://github.com/robotframework/robotframework)
   - 直接下载exe文件安装
   - 命令行安装：pip install robotframework 或者 下载源文件后，Python setup.py install
   安装成功后，cmd中输入pybot --version，可看到是否安装成功。

4. Selenium、AutoItLibrary等各种支持库的安装方法：
	[Selenium2library库](http://rtomac.github.io/robotframework-selenium2library/doc/Selenium2Library.html)：easy_install robotframework-selenium2library
	[Robotframework-Database-Library库](http://franz-see.github.io/Robotframework-Database-Library/):easy_install robotframework-databaselibrary

5. 各浏览器driverserver的安装：
   - 将[chromedriver.exe](https://code.google.com/p/selenium/wiki/ChromeDriver)和IEDriverServer.exe放在D:\Python27或者D:\Python27\Scripts目录下,因为这两个目录已经添加到系统环境变量里面了
   - 火狐不需要装driverserver；脚本中浏览器参数不填写时默认为火狐，火狐浏览器版本支持火狐27以下

6. 安装robot sublime的插件：
     https://github.com/shellderp/sublime-robot-plugin

参考:
	http://blog.csdn.net/xg_1020/article/details/37595415 
	http://blog.csdn.net/ren911/article/details/5576310



