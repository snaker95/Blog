---
title: FIND_IN_SET 的漂亮使用场景
categories: MySql
tags: MySql
keywords: [Mysql,FIND_IN_SET]
description: FIND_IN_SET 的漂亮使用场景

---

[TOC]

# FIND_IN_SET 的漂亮使用场景
　　在数据库中,可能会出现一对多的情况, 有时候数据库就会设计这么一个字段来记录该情况, **例如:** 该字段(`father`)记录自己的所属分类:`１，２，３４，６７`,说明当前自己属于`１`／`２`／`３４`／`６７`的分类；
　　在一定的场景下，　需要查询某一分类下的所有子级，　例如：查询所有`67`分类下的所有子类, 这样的数据库看起来好像很蛋疼了.... 但是不要怕,今天一不小心发现了一个**MySQL**的高级函数(`FIND_IN_SE`T) , 类是与 IN, 

```text
mysql>  select * from table where FIND_IN_SET(67,father)
# father 为 table表中的字段
```

** 重点 **
`FIND_IN_SET` 虽然只是`SET`类型的函数, 但是经测试, `varchar`也是可以的, 只要保持格式;
至于效率问题, 还是有待考察的......

Author [@Snaker95][1]

[1]: http://www.sharedsea.com