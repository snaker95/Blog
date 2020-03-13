---
title: MySql生成默认数据
categories: MySql
tags: MySql
keywords: [Mysql,数据库,初始化]
description: 某些场景下,MySql安装完成后却发现不能连接,可能造成的原因是没有MySql初始化时的数据库

---

[TOC]

# MySql生成默认数据
某些场景下,MySql安装完成后却发现不能连接,可能造成的原因是没有MySql初始化时的数据库,具体初始化MySql数据库方法如下:
<!--more-->

```sh
 [root@host~]$ cd /usr/bin
 [root@host~]$ mysql_install_db
```

**注 : **如果在/usr/bin下没有mysql_install_db,可通过find / -name mysql_install_db 进行查找

Author [@Snaker95][1]

[1]: http://www.sharedsea.com