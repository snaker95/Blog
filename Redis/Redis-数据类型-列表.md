---
title: Redis-数据类型-列表
date: 2016-06-08 11:28:12
categories: Redis   
tags: Redis
keywords: [Redis,list]
description: Redis的数据类型-List(列表)

---

[TOC]

# Redis-数据类型-列表
  Redis 数据库是key-value类型存储方式, 支持的数据类型有 `字符串`(string)、 `哈希`(Hash)、 `列表`(list)、 `集合`(set)、 `有序集合`(Zset). 
  现在学习一下**列表**类型的相关操作


**1. 移出并获取列表的第一个元素 `blpop`**
```
 BLPOP key1 [key2 ... ] timeout 
```
>  **注:** 如果`列表`没有元素会**阻塞**列表直到等待超时或发现可弹出元素为止。

**2. 移出并获取列表的最后一个元素 `brpop`**
```
 BRPOP key1 [key2 ... ] timeout
```
>  **注:** 如果`列表`没有元素会**阻塞**列表直到等待超时或发现可弹出元素为止。

**3. 弹出一个元素插入另一个列表 `brpoplpush`**
```
 BRPOPLPUSH source destination timeout
```
 **注:**从`列表`中弹出一个值，将弹出的元素插入到另外一个列表中并返回它；如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。

**4. 通过索引获取列表中的元素 `lindex`**
```
 LINDEX key index  # 获取key中索引index元素
```

**5. 在列表的元素`前`和`后`插入元素 `linsert`**
```
 LINSERT key BEFORE old-value value  # 在old-value之前插入value
 LINSERT key AFTER old-value value  # 在old-value之后插入value 
```

**6. 获取列表长度 `llen`**
```
 LLEN key
```

**7. 移出并获取列表的第一个元素 `lpop`**
```
 LPOP key
```

**8. 在列表头部插入多个值 `lpush`**
```
 LPUSH key value [value1 value2 ... ]
```

**9. 在已存在的列表头部插入多个值 `lpushx`**
```
 LPUSHX key value [value1 value2 ... ]
```

**10. 获取列表指定范围内的元素 `lrange`**
```
 LRANGE key start end  # 获取key中的start到end的元素
```

**11. 移除列表元素 `lrem`**
```
 LREM key count value  # 移出value
```
>  **注:** count > 0 : 从表头开始向表尾搜索，移除与 VALUE 相等的元素，数量为 COUNT ;
          count < 0 : 从表尾开始向表头搜索，移除与 VALUE 相等的元素，数量为 COUNT 的绝对值
          count = 0 : 移除表中所有与 VALUE 相等的值.

**12. 通过索引设置列表元素的值 `lset`**
```
 LSET key index value  # 设置key的索引index的值为value
```

**13. 修剪列表 `ltrim`**
```
 LTRIM key start stop
```
>  **注:** 列表只保留指定区间内的元素，不在指定区间之内的元素都将被删除。

**14. 移除并获取列表最后一个元素 `rpop`**
```
 RPOP key 
```

**15. 在列表中添加N个值 `rpush`**
```
 RPUSH key value [value1 value2 ... ]
```

**16. 为已存在的列表添加值 `rpushx`**
```
 RPUSHX key value
```

Author [@Snaker95][1]

[1]: http://www.sharedsea.com


