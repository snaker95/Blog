---

title: MySql命令导入导出数据库
categories: MySql
tags: MySql
keywords: [Mysql,导入,导出]
description: MySql命令行导入与导出数据库,并且可以重新编码

---

[TOC]

# MySql命令导入导出数据库
数据库备份和恢复是工作中不可缺少的, 虽然现在已经有很多图像工具可以帮助我们很容易的实现, 但当在对原有数据库进行**重新编码**导入导出的场景下 ,工具就显得很捉襟见肘, 其实, 命令行的导入导出很容易就解决了...

<!--more-->

## 1. 数据库导出

> [root@host~]$  mysqldmp -uroot -p -R -default-character-set=utf8 DATABASENAME > /path/Sqlname




## 2. 数据库导入

> [root@host~]$  mysql -uroot -p --default-character-set=utf8 DATABASENAME < /path/Sqlname

Author [@Snaker95][1]

[1]: http://www.sharedsea.com