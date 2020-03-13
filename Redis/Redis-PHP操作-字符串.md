---
title: Redis-PHP操作-字符串
categories: Redis   
tags: Redis
keywords: [Redis,PHP,string]
description: Redis的PHP操作-String(字符串)

---

[TOC]

# Redis-PHP操作-字符串
  Redis 数据库是key-value类型存储方式, 支持的数据类型有 `字符串`(string)、 `哈希`(Hash)、 `列表`(list)、 `集合`(set)、 `有序集合`(Zset). 

  现在学习一下**字符串**类型的`PHP`相关操作
## 一. PHP连接Redis
``` php
// 连接Redis CI框架
function __connect_redis() {
    // 引进redis配置文件
    if($this->config->load('redis', TRUE, TRUE)){
        $this->redis = new Redis();
        try
        {
            $config = $this->config->item('redis');
            if ($config['socket_type'] === 'unix')
            {
                $success = $this->redis->connect($config['socket']);
            }
            else // tcp socket
            {
                $success = $this->redis->connect(
						$config['host']
						,$config['port']
						,$config['timeout']
						);
            }

            if ( ! $success)
            {
                log_message('error'
                    ,'Cache: Redis connection failed. Check your configuration.'
                 );
            }
            /* ---- 密码认证 ---- */
            if (isset($config['password']) &&
                ! $this->redis->auth($config['password'])
               )
            {
                log_message('error'
                    ,'Cache: Redis authentication failed.'
                 );
            }
            /* ---- 选择数据库 ---- */
            if (isset($config['database']) &&
                ! $this->redis->select($config['database'])
               )
            {
                log_message('error'
                    ,'Cache: Redis select database failed.'
                 );
            }
        }
        catch (RedisException $e)
        {
            log_message('error'
                ,'Cache: Redis connection refused ('.$e->getMessage().')'
             );
        }
    }else{
        return false;
    }
}

```
## 二. PHP操作Redis
**1. 设置数据 `set`**
``` php
 /*@param key 
  *@param test value
  *@return boolean
  */
 $this->redis->set('key', 'test'); // 返回boolean
```

**2. 获取数据 `get`**
``` php
 /*@param key 
  *@return value
  */
 $this->redis->get('key');
```

**3. 获取字符串中的一段 `getrange` OR `substr`**
``` php
 /*@param key 
  *@param start
  *@param end
  *@return string
  */
 $this->redis->getrange($key, $start, $end);
```
**讲解：**　当＄start或者$end为负数时,从尾部开始

**4. 设置并获取数据 `getset`**
``` php
  /*@param key 
  *@param value
  *@return string
  */
 $this->redis->getset($key, $value);
```

**5. 获取指定偏移量上的位 `getbit`**
``` php
 /*# 获取指定key中字符串指定偏移量上的位(bit) 
  *@param key 
  *@param offset
  *@return string
  */
 $this->redis->getbit($key, $offset);
```
>  **注:**当 offset 比字符串值的长度大，或者 key 不存在时，返回 0

**6. 设置或清除指定偏移量上的位 `setbit`**
``` php
 /*# 设置或清除指定key中字符串指定偏移量的位(bit)
  *@param key 
  *@param offset
  *@return string
  */
 $this->redis->setbit($key, $offset, $value);
```

**7. 获取所有给定key的值 `mget`**
``` php
  /*# 获取所有(一个或多个)给定 key 的值。
  *@param key 
  *@return string
  */
 $this->redis->mget($key1, $key2);
```

**8. 设置带过期时间的数据(单位:*秒*) `setex`**
``` php
 /*# 设置key的值,并指定过期时间seconds(秒)
  *@param key 
  *@param timeout
  *@param value
  *@return boolean
  */
 $this->redis->setex($key, $timeout, $value);
```

**9. 不存在key时设置数据 `setnx`**
``` php
 /*# 只有key不存在时,设置key的值
  *@param key 
  *@param test value
  *@return boolean
  */
 $this->redis->setnx('key', 'test'); // 返回boolean
```

**10. 覆盖key中的数据(类是字符串替换) `setrange`**
``` php
 /*# 用value覆盖key中从offset偏移之后的数据
  *@param key 
  *@param offset
  *@param value
  *@return int 字符串长度
  */
 $this->redis->setrange($key, $offset, $value); // 返回boolean
```
>  **注:**超过部分直接显示

**11. 获取key中字符串长度 `strlen`**
``` php
 /*@param key 
  *@return value
  */
 $this->redis->strlen('key');
```

**12. 设置多个key-value数据 `mset`**
``` php
 /*@param arr 关联数组(key=>value) 
  *@return boolean
  */
 $this->redis->mset($arr); // 返回boolean
```

**13. 设置多个ke-value数据,仅当key不存在 `msetnx`**
``` php
 /*@param arr 关联数组(key=>value) 
  *@return boolean
  */
 $this->redis->msetnx($arr); // 返回boolean
```

**14. 设置带过期时间的数据(单位:*毫秒*) `psetex`**
``` php
 /*# 设置key的值,milliseconds(毫秒), 
  *@param key 
  *@param milliseconds
  *@param value
  *@return boolean
  *@不成功, undefined method Redis::psetex()
  */
 $this->redis->psetex($key, $milliseconds, $value);
```

**15. 数字自增一 `incr`**
``` php
 /*# key中储存的数字值增加1
  *@param key 
  *@return value
  */
 $this->redis->incr('key');
```

**16. 数字自增N `incrby`**
``` php
 /*# key中储存的数字值增加N
  *@param key 
  *@param N 
  *@return value
  */
 $this->redis->incrby('key',$N);
```

**17. 数字自增浮点型 `incrbyfloat`**
``` php 
 /*# key中储存的数字增加 N.M
  *@param N.M
  *@return value
  */
 $this->redis->incrbyfloat('key',$N);
```

**18. 数字自减一 `decr`**
``` php
 /*# key中储存的数字值自减1
  *@param key 
  *@return value
  */
 $this->redis->decr('key');
```

**19. 数字自增N `decrby`**
``` php
 /*# key中储存的数字值减N
  *@param N
  *@return value
  */
 $this->redis->decrby('key',$N);
```

**20. 追加数据 `append`**
``` php
 /*# 在key值末尾追加value
  *@param key 
  *@param value
  *@return int 字符串数量
  */
 $this->redis->append($key, $value);
```

**21. 删除数据 `delete`**
``` php
 /*
  *@param key 
  *@return boolean
  */
 $this->redis->delete($key1, $key2);
 $this->redis->delete(array($key1, $key2));
```

**21. 检测key是否存在 `exists`**
``` php
 /*
  *@param key 
  *@return boolean
  */
 $this->redis->exists($key);
```

Author [@Snaker95][1]

[1]: http://www.sharedsea.com


