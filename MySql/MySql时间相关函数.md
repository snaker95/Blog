---

title: MySql时间相关函数
categories: MySql
tags: MySql
keywords: [Mysql,时间,函数]
description: MySql获取时间的函数以及相关转换函数

---

[TOC]

# MySql时间相关函数
Mysql是大多程序猿选择的数据库,在查询的时候可能需要对时间进行获取或者时间格式化

<!--more-->

## 1. 获取当前时间:

	`SELECT CURDATE()` : 仅获取 YYYY-MM-DD

	`SELECT NOW()`	:	YYYY-MM-DD HH:ii:ss

## 2. 转化单个**年**\/**月**\/**日**\/**日**

	`YEAR()`	`MONTH()`	`DAY()`	 `HOUR`

## 3. Sql语句计算年龄

	`SELECT YEAR(curdate()) - YEAR('1990-01-09')  as age;`

或者:

	`SELECT LEFT(curdate(),4) - LEFT('1990',4) as age`

	*注 : LEFT() : 从左侧开始取值, RIGHT() : 从右侧开始取值*
	
Author [@Snaker95][1]

[1]: http://www.sharedsea.com