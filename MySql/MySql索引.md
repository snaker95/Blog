---
title: MySql索引
categories: MySql
tags: MySql
keywords: [Mysql,索引]
description: MySql索引的使用和优化

---

[TOC]

# MySql索引

## 索引的概念

索引是一种特殊的文件(InnoDB数据表上的索引是表空间的一个组成部分)，它们包含着对数据表里所有记录的引用指针, 包含着对数据表里所有记录的引用指针. （注：一般数据库默认都会为主键生成索引）

* 索引分为`聚簇索引`和`非聚簇索引`两种，`聚簇索引`是按照数据存放的物理位置为顺序的，而`非聚簇索引`就不一样了；聚簇索引能提高多行检索的速度，而非聚簇索引对于单行的检索很快。

## 索引类型

### 1. 普通索引 index

```Mysql
-- 创建索引
CREATE INDEX index_name ON table(column(length))
-- 修改索引
ALTER TABLE table_name ADD INDEX index_name ON (column(length))
-- 删除索引
DROP INDEX index_name ON table
```

### 2. 唯一索引 unique index

```Mysql
-- 创建索引
CREATE UNIQUE INDEX index_name ON table(column(length))
-- 修改索引
ALTER TABLE table_name ADD UNIQUE index_name ON (column(length))
```

### 3. 全文索引 fulltext

MySQL从3.23.23版开始支持全文索引和全文检索，FULLTEXT索引仅可用于 MyISAM 表；

不过切记对于大容量的数据表，生成全文索引是一个非常消耗时间非常消耗硬盘空间的做法;

## 索引分类

### 1. 单列索引

理论上每张表里面最多可创建16个索引

### 2. 组合索引

组合索引遵循“最左前缀”规则



## 索引优化

* 索引不会包含有NULL值的列; 
  * 只要列中包含有NULL值都将不会被包含在索引中，复合索引中只要有一列含有NULL值，那么这一列对于此复合索引就是无效的
* 使用短索引
  * 对串列进行索引，如果可能应该指定一个前缀长度
* 索引列排序
  * MySQL查询只使用一个索引，因此如果where子句中已经使用了索引的话，那么order by中的列是不会使用索引的
* like语句操作
  * 一般情况下不鼓励使用like操作，如果非使用不可，如何使用也是一个问题。like “%aaa%” 不会使用索引而like “aaa%”可以使用索引
* 不要在列上进行运算

[参考](https://www.cnblogs.com/doudouxiaoye/p/5831449.html)



Author [@Snaker95][1]

[1]: http://www.sharedsea.com