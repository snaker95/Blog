---
title: sql语句分析和优化过程
categories: MySql
Tags: MySql
keywords: [Mysql,优化]
description: MySQL sql语句分析和优化过程

---

[TOC]
# MySQL sql语句分析和优化过程
  对于操作数据库的猿类来说, 每完成一次数据库操作都需要考虑到是否实现了要求, 执行效率是否高效......在这里, 将简单学习下SQL的分析过程以及简单优化方法
## 查看SQL的执行频率
  * 相关命令: `show status`
  * 命令讲解: `show [ global | session ] status [like options]`
  * * 默认为`session`级别(当前session); `global`: 为全局有效
  * * `[like options]`: 为过过滤插询
  * 查询结果:
  * * 以`Com_`为前缀的 增删改查
  * * 以`Innodb_rows_`为前缀的相关信息
  * * 数据库的基本情况:
  * * `Connections`: 试图连接MySQL服务器的次数
  * * `Uptime`: 服务器的工作时间
  * * `Slow_queries`: 慢查询次数

## 定位执行效率低的SQL
  * 开启慢查询日志 (缺点: 数据收集时间较长)

  * ```mysql
    show variables like 'slow_query%';
    ```

  * 使用`show processlist`命令查看当前MySQL正在进行的线程情况

## 使用`EXPLAIN` 分析SQL

## 使用`show profiles`分析SQL
### 基本步骤:
    1. `set profiling=1;`
    2. `show profiles;`
    3. `show profile for query 3;`
    4. `set profiling=0;`

#### Tips

1. 加了索引，依然全表扫描的可能情况有：
   * 索引列为字符串，而没带引号；
   * 索引列没出现在where条件后面；
   * 索引列出现的位置没在前面。

2. 关联查询不走索引的可能情况有：
   * 关联的多张表的字符集不一样；
   * 关联的字段的字符集不一样；
   * 存储引擎不一样；
   * 字段的长度不一样