---
title: Redis-PHP操作-列表
categories: Redis   
tags: Redis
keywords: [Redis,PHP,list]
description: Redis的数据类型-List(列表)

---

[TOC]

# Redis-PHP操作-列表
  Redis 数据库是key-value类型存储方式, 支持的数据类型有 `字符串`(string)、 `哈希`(Hash)、 `列表`(list)、 `集合`(set)、 `有序集合`(Zset). 
  现在学习一下**列表**类型的`PHP`相关操作


**1. 移出并获取列表的第一个元素 `blpop`**
``` php
 /*@param key 
  *@param timeout
  *@return array(key,value)
  */
 $this->redis->blpop($key, $timeout);
```
>  **注:** 如果`列表`没有元素会**阻塞**列表直到等待超时或发现可弹出元素为止。

**2. 移出并获取列表的最后一个元素 `brpop`**
``` php
 /*@param key 
  *@param timeout
  *@return array(key,value)
  */
 $this->redis->brpop($key, $timeout);
```
>  **注:** 如果`列表`没有元素会**阻塞**列表直到等待超时或发现可弹出元素为止。

**3. 弹出一个元素插入另一个列表 `brpoplpush`**
``` php
 /*@param sourceKey
  *@param desKey
  *@param timeout
  *@return value
  */
 $this->redis->brpoplpush($sourceKey, $desKey, $timeout);
```
 **注:**从`列表`中弹出一个值，将弹出的元素插入到另外一个列表中并返回它；如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。

**4. 通过索引获取列表中的元素 `lindex` `lget`**
``` php
 /*@param key 
  *@param index
  *@return value
  */
 $this->redis->lindex($key, $index);
 $this->redis->lget($key, $index);
```

**5. 在列表的元素`前`和`后`插入元素 `linsert`**
``` php
 /*@param key 
  *@param Redis::AFTER/Redis::BEFORE
  *@return oldValue
  *@return int 列表长度
  */
 $this->redis->linsert($key, Redis::AFTER, $old, $value);
 $this->redis->linsert($key, Redis::BEFORE, $old, $value); 
```
**注:** 在最近的old-value`前`或者`后`添加value

**6. 获取列表长度 `llen`**
``` php
 /*@param key 
  *@return int 列表长度
  */
 $this->redis->llen($key);
```

**7. 移出并获取列表的第一个元素 `lpop`**
```php
 /*@param key 
  *@return value
  */
 $this->redis->lpop($key);
```

**8. 在列表头部插入一个值 `lpush`**
```php
 /*@param key
  *@param value 
  *@return int 列表长度
  */
 $this->redis->lpush($key, $value);
```

**9. 在已存在的列表头部插入一个值 `lpushx`**
``` php
 /*@param key
  *@param value 
  *@return int 列表长度, 当key不存在返回0
  */
 $this->redis->lpushx($key, $value);
```

**10. 获取列表指定范围内的元素 `lrange`**
``` php
 /*@param key
  *@param start
  *@param end -1:为尾部
  *@return array(v1, v2, ...)
  */
 $this->redis->lrange($key, $start, $end);
```

**11. 移除列表元素 `lrem`**
``` php
 /*@param key
  *@param value
  *@param count
  *@return array(v1, v2, ...)
  */
 $this->redis->lrem($key, $value, $count);
```
>  **注:** count > 0 : 从表头开始向表尾搜索，移除与 VALUE 相等的元素，数量为 COUNT ;
          count < 0 : 从表尾开始向表头搜索，移除与 VALUE 相等的元素，数量为 COUNT 的绝对值
          count = 0 : 移除表中所有与 VALUE 相等的值.

**12. 通过索引设置列表元素的值 `lset`**
``` php
 /*@param key
  *@param index
  *@param value
  *@return array(v1, v2, ...)
  */
 $this->redis->lset($key, $index, $value);
```

**13. 修剪列表 `ltrim`**
```php
 LTRIM key start stop
 /*@param key
  *@param start
  *@param stop
  *@return array(v1, v2, ...)
  */
 $this->redis->ltrim($key, $start, $stop);
```
>  **注:** 列表只保留指定区间内(start-stop)的元素，不在指定区间之内的元素都将被删除。

**14. 移除并获取列表最后一个元素 `rpop`**
```php
 /*@param key 
  *@return value
  */
 $this->redis->rpop($key);
```

**15. 在列表中添加一个值 `rpush`**
```php
 /*@param key 
  *@return value
  */
 $this->redis->rpush($key);
```

**16. 为已存在的列表添加值 `rpushx`**
```php
 /*@param key 
  *@return value
  */
 $this->redis->rpushx($key);
```

Author [@Snaker95][1]

[1]: http://www.sharedsea.com


