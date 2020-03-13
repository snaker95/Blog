---
title: Redis-数据类型-集合
date: 2016-06-08 11:28:16
categories: Redis   
tags: Redis
keywords: [Redis,set]
description: Redis的数据类型-set(列表)

---

[TOC]

# Redis-数据类型-集合
  Redis 数据库是key-value类型存储方式, 支持的数据类型有 `字符串`(string)、 `哈希`(Hash)、 `列表`(list)、 `集合`(set)、 `有序集合`(Zset). 

  现在学习一下**列表**类型的相关操作
  
### 1. SADD 添加成员到集合中
``` sh
$ redis-server > SADD key member [member  ...] 
    
```
> **(1)** 将一个或多个member元素加入到集合key当中，已经存在于集合的member元素将被忽略。
> **(2)** 假如key不存在，则创建一个只包含member元素作成员的集合。
> **(3)** 当key不是集合类型时，返回一个错误。
> **(4)** 返回值: 被添加到集合中的新元素的数量，不包括被忽略的元素。

### 2. SREM 移除集合中的成员
```sh
$ redis-server > SREM key member [member ...]
```
> **(1)** 移除集合key中的一个或多个member元素，不存在的member元素会被忽略。
> **(2)** 当key不是集合类型，返回一个错误。
> **(3)** 返回值:被成功移除的元素的数量，不包括被忽略的元素。

### 3. SMEMBERS 获取集合中所有成员
```sh
$ redis-server > SMEMBERS key
```
> **(1)** 返回值: 所有的成员

### 4. SISMEMBER 判断某成员是否在集合中
```sh 
$ redis-server > SISMEMBER key member
```
> **(1)** 判断某成员是否在集合中
> **(2)** 返回值: 1=> 存在, 0 => 不存在

### 5. SCARD 获取集合的成员数量
```sh
$ redis-server > SCARD key
```
> **(1)** 获取集合的长度
> **(2)** 当key不存在时，返回0。

### 6. SMOVE 从原集合移动成员到目的集合
```sh
$ redis-server > SMOVE source destination member
```
SMOVE是原子性操作。

> **(1)** 如果source集合不存在或不包含指定的member元素，则SMOVE命令不执行任何操作，仅返回0。否则，member元素从source集合中被移除，并添加到destination集合中去。
> **(2)** 当destination集合已经包含member元素时，SMOVE命令只是简单地将source集合中的member元素删除。
> **(3)** 当source或destination不是集合类型时，返回一个错误。
> **(4)** 返回值:
> ** 如果member元素被成功移除，返回1。
> ** 如果member元素不是source集合的成员，并且没有任何操作对destination集合执行，那么返回0。

### 7. SPOP 随机弹出集合中的一个成员
```sh
$ redis-server > SPOP key
```
> **(1)** 随机删除集合中的一个成员,并返回该成员
> **(2)** 返回值:
> ** 被移除的随机元素。
> ** 当key不存在或key是空集时，返回nil。

### 8. SRANDMEMBER 随机返回一个成员
```sh
$ redis-server > SRANDMEMBER key
```
> **(1)** 仅随机返回一个成员
> **(2)** 被选中的随机元素。当key不存在或key是空集时，返回nil。

### 9. SINTER 获取数个集合的交集, 并组成新的成员列表
```sh
$ redis-server > SINTER key [key ...]
```
> **(1)** 返回一个集合的全部成员，该集合是所有给定集合的交集。
> **(2)** 不存在的key被视为空集。
> **(3)** 返回值: 交集成员的列表。

### 10. SINTERSTORE 获取数个集合的交集,并保存到新的集合中
```sh
$ redis-server > SINTERSTORE destination key [key ...]
```
> **(1)** 此命令等同于SINTER，但它将结果保存到destination集合，而不是简单地返回结果集。
> **(2)** 如果destination集合已经存在，则将其覆盖。
> **(3)** destination可以是key本身。
> **(4)** 返回值: 新集合的长度

### 11. SUNION 获取数个集合的并集, 返回列表
```sh
$ redis-server > SUNION key [key ...]
```
> **(1)** 返回一个集合的全部成员，该集合是所有给定集合的并集。
> **(2)** 不存在的key被视为空集。
> **(3)** 返回值: 并集成员的列表。

### 12. SUNIONSTORE 获取数个集合的并集, 并保存到新的集合中
```sh 
$ redis-server > SUNIONSTORE destination key [key ...]
```
> **(1)** 此命令等同于SUNION，但它将结果保存到destination集合，而不是简单地返回结果集。
> **(2)** 如果destination集合已经存在，则将其覆盖。
> **(3)** destination可以是key本身。
> **(4)** 返回值: 新集合的长度

### 13. SDIFF 获取集合间的差集
```sh
$ redis-server > SDIFF key [key ...]
```
> **(1)** 返回一个集合的全部成员，该集合是所有给定集合的差集。
> **(2)** 不存在的key被视为空集。
> **(3)** 返回值: 差集成员的列表。

### 14. SDIFFSTORE 获取集合间的差集, 并保存到新的集合中
```sh
$ redis-server > SDIFFSTORE destination key [key ...]
```
> **(1)** 此命令等同于SDUFF，但它将结果保存到destination集合，而不是简单地返回结果集。
> **(2)** 如果destination集合已经存在，则将其覆盖。
> **(3)** destination可以是key本身。
> **(4)** 返回值: 新集合的长度

Author [@Snaker95][1]

[1]: http://www.sharedsea.com


