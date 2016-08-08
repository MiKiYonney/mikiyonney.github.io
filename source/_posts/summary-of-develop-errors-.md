title: Summary of develop errors 
date: 2014-04-01 17:58:16
tags:
categories: Java
---

### hibernate配置文件中大小写问题
遇到过两次，在windows下项目能够正常启动, 在linux下项目不能够正常启动, 报 xx.hbm.xml不存在.
Linux下文件区分大小写，window下文件不区分大小写，譬如：

	<class name="com.mckj.entity.QuickasProjectrepairreq" table="quickas_projectrepairreq" catalog="wyn">
如table的名称在mysql数据库中默认会为小写，windows下反向工程可能会按照驼峰生成为quickas_projectRepairReq,这样在windows下正常，在linux下就会报错了！改成小写即可~



