---

title: MySql权限设置
categories: MySql
tags: MySql
keywords: [Mysql,权限]

---

[TOC]

# MySql权限设置
在开发过程中, 为了保证各程序猿所得到的数据一致, 通常采用数据库服务器, 即连接同一个数据库, 所以为不同的角色设置不同的数据库权限是无可厚非的. 

> 通用方法

```sh
[root@host~] GRANT ALL ON *.* TO 'username'@'host' IDENTIFEID BY 'password'
```

<!--more-->

**讲解:**

> `ALL` 表示所有权限, 其中包括:

>> * **普通**数据库用户相关权限: `select`、`insert`、`update`、`delete`

>> * **开发**数据库人员相关权限: `create`、`alert`、`drop`

>> * **外键**权限: `references`

>> * **索引**权限: `index`

>> * **视图**相关权限: `create view`、`show view`

> `*.*` 表示所有**数据库**中的所有**数据表**

> `username` 表示**用户名称**

> `host` 表示主机名称,**%**表示所有,另外,使用**IP地址**作为host

> `password` 表示当前`username`连接数据库的**密码**

Author [@Snaker95][1]

[1]: http://www.sharedsea.com