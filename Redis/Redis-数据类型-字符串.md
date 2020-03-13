---
title: Redis-数据类型-字符串
date: 2016-06-08 11:28:10
categories: Redis   
tags: Redis
keywords: [Redis,string]
description: Redis的数据类型-String(字符串)

---

[TOC]

# Redis-数据类型-字符串
  Redis 数据库是key-value类型存储方式, 支持的数据类型有 `字符串`(string)、 `哈希`(Hash)、 `列表`(list)、 `集合`(set)、 `有序集合`(Zset). 

  现在学习一下**字符串**类型的相关操作
**1. 设置数据 `set`**
``` php
 SET key value  # 设置指定key的值 
```

**2. 获取数据 `get`**
``` php
 GET key	# 获取指定key的值
```

**3. 获取字符串中的一段 `getrange`**
``` php
 GETRANGE key start end 	# 获取指定key从start到end的字符串
```

**4. 设置并获取数据 `getset`**
``` php
 GETSET key value 	# 设置指定key的值为value, 并返回key的旧值
```

**5. 获取指定偏移量上的位 `getbit`**
``` php
 GETBIT key offset 	# 获取指定key中字符串指定偏移量上的位(bit) 
```
>  **注:**当 offset 比字符串值的长度大，或者 key 不存在时，返回 0

**6. 设置或清除指定偏移量上的位 `setbit`**
``` php
 SETBIT key offset value  # 设置或清除指定key中字符串指定偏移量的位(bit)
```

**7. 获取所有给定key的值 `mget`**
``` php
 MGET key1 [key2...] 	# 获取所有(一个或多个)给定 key 的值。
```

**8. 设置带过期时间的数据(单位:*秒*) `setex`**
``` php
 SETEX key seconds value # 设置key的值,并指定过期时间seconds(秒)
```

**9. 不存在key时设置数据 `setnx`**
``` php
 SETNX key value 	# 只有key不存在时,设置key的值
```

**10. 覆盖key中的数据(类是字符串替换) `setrange`**
``` php
 SETRANGE key offset value 	# 用value覆盖key中从offset偏移之后的数据
```
>  **注:**超过部分直接显示

**11. 获取key中字符串长度 `strlen`**
``` php
 STRLEN key
```

**12. 设置多个key-value数据 `mset`**
``` php
 MSET key value [key1 value1 ...]
```

**13. 设置多个ke-value数据,仅当key不存在 `msetnx`**
``` php
 MSETNX key value [key1 value1 ...]
```

**14. 设置带过期时间的数据(单位:*毫秒*) `psetex`**
``` php
 PSETEX key milliseconds value 	# (毫秒)
```

**15. 数字自增一 `incr`**
``` php
 INCR key 	# key中储存的数字值增加1
```

**16. 数字自增N `incrby`**
``` php
 INCRBY key N 	# key中储存的数字值增加N
```

**17. 数字自增浮点型 `incrbyfloat`**
``` php 
 INCRBYFLOAT key N.M 	# key中储存的数字增加 N.M
```

**18. 数字自减一 `decr`**
``` php
 DECR key 	# key中储存的数字值自减1
```

**19. 数字自增N `decrby`**
``` php
 DECRBY key N 	# key中储存的数字值减N
```

**20. 追加数据 `append`**
``` php
 APPEND key value 	# 在key值末尾追加value
```

Author [@Snaker95][1]

[1]: http://www.sharedsea.com


