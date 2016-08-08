title: mysql pagination optimize
date: 2015-5-10 11:43:40
tags: [DB Optimize]
categories: DB
---

mysql分页就直接使用limit进行操作,limit如果我们直接不加任何处理可能数据大了就会很卡的。
<!-- more -->

最常见MYSQL最基本的分页方式：

	select * from content order by id desc limit 0, 10

在中小数据量的情况下,这样的SQL足够用了,唯一需要注意的问题就是确保使用了索引。随着数据量的增加,页数会越来越多,查看后几页的SQL就可能类似：

    select * from content order by id desc limit 10000, 20

limit 10000,20的意思扫描满足条件的10020行,扔掉前面的10000行,返回最后的20行,问题就在这里,如果是limit 100000,100,需要扫描100100行,在一个高并发的应用里,每次查询需要扫描超过10W行,性能肯定大打折扣。提到limitn性能是没问题的,因为只扫描n行。

也就是说,就是越往后分页,LIMIT语句的偏移量就会越大,速度也会明显变慢。怎么解决？


** 1.“clue”, 给翻页提供一些”线索”。 **

比如还是

	SELECT * FROM message ORDER BY id DESC

按id降序分页,每页20条,当前是第10页,当前页条目id最大的是9527,最小的是9500,如果我们只提供”上一页”、”下一页”这样的跳转(不提供到第N页的跳转),那么在处理”上一页”的时候SQL语句可以是：

	SELECT * FROM message WHERE id > 9527 ORDER BY id ASC LIMIT 20;

不管翻多少页,每次查询只扫描20行。但是这种方法的缺点是只能提供”上一页”、”下一页”的链接形式,但是我们的产品经理非常喜欢”<上一页 1 2 3 4 5 6 7 8 9 下一页>”这样的链接方式,怎么办呢？

** 2.limit m,n 不可避免情况 **

如果LIMIT m,n不可避免的话,要优化效率,只有尽可能的让m小一下,我们扩展前面”clue”做法,还是 SELECT * FROM message ORDER BY id DESC,按id降序分页,每页20条,当前是第10页,当前页条目id最大的是9527,最小的是9500,比如要跳到第8页,SQL语句可以这样写：

	SELECT * FROM message WHERE id > 9527 ORDER BY id ASC LIMIT 20,20;

跳转到第13页：

	SELECT * FROM message WHERE id < 9500 ORDER BY id DESC LIMIT 40,20;

** 3.使用子查询来操作 **

子查询的分页方式来提高分页效率,可用SQL语句如下：

	SELECT * FROM `content` WHERE id <= (SELECT id FROM `content` ORDER BY id desc LIMIT ".($page-1)*$pagesize.", 1) ORDER BY id desc LIMIT $pagesize

子查询是在索引上完成的,而普通的查询时在数据文件上完成的,通常来说,索引文件要比数据文件小得多,所以操作起来也会更有效率。通过explain SQL语句发现：子查询使用了索引！

代码如下

	id select_type table type possible_keys key key_len ref rows Extra

	1  PRIMARY content range PRIMARY PRIMARY 4 NULL 6264 Using where

	2  SUBQUERY content index NULL PRIMARY 4 NULL 27085 Using index

经过测试,使用子查询的分页方式的效率比纯LIMIT提高了14-20倍！

** 4. JOIN分页方式 **

	SELECT * FROM `content` AS t1 JOIN (SELECT id FROM `content` ORDER BY id desc LIMIT ".($page-1)*$pagesize.", 1) AS t2 WHERE t1.id <= t2.id ORDER BY t1.id desc LIMIT $pagesize;

经过测试,join分页和子查询分页的效率基本在一个等级上,消耗的时间也基本一致。explain SQL语句：

代码如下

	id select_type table type possible_keys key key_len ref rows Extra

	1 PRIMARY <derived2> system NULL NULL NULL NULL 1  

	1 PRIMARY t1 range PRIMARY PRIMARY 4 NULL 6264 Using where

	2 DERIVED content index NULL PRIMARY 4 NULL 27085 Using index


