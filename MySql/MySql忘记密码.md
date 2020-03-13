---
title: MySql忘记密码
categories: MySql
tags: MySql
keywords: [Mysql,密码]
description: MySql忘记密码解决方案

---

[TOC]

# MySql忘记密码
Mysql是大多程序猿选择的数据库,可是,由于各种场景下,往往忘记数据库的密码而导致业务工作无法进行,所以...
<!--more-->
## 1. 编辑my.ini配置文件
> 在`[mysqld]`的下方添加一句: `skip-grant-tables`;

## 2. 重启MySql服务
> Linux:  `server mysqld restart`
> 
> Windows: 通过`任务管理器`重启MySql

## 3. 登录MySql
> ```
 mysql -uroot;

```text
## 4. 修改密码
> ```
 use mysql;
 update user set Password = password('新密码') where User = 'root'
> ```  

## 5. 重启MySql服务 
	( 详见 2. )
	
Author [@Snaker95][1]

[1]: http://www.sharedsea.com



```