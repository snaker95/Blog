---
title: Redis-PHP操作
categories: Redis   
tags: Redis
keywords: [Redis]
description: PHP连接Redis的安装及相关操作

---

[TOC]

# Redis-PHP操作
PHP连接Redis的安装及相关操作
<!--more-->
## 一. 安装Redis
> 下载地址: [Redis](http://www.redis.net.cn/download/)

## 二. PHP安装Redis扩展
> 1. 下载PHP扩展**phpredis**对应版本的**源码包**, 下载地址: [phpredis](https://github.com/phpredis/phpredis)
> 2. 查看是否有`phpize`命令, 若没有, 请安装`php-devel`, **yum install php-devel**;
> 3. 解压进入phpredis目录, 执行`phpize`, 执行 `./configure --with-php-config = /path/php-config`
> 4. 在php.ini配置文件中, 添加扩展
```
 extension=redis.so // 或者写绝对路径
```

## 三. PHP连接Redis服务
``` php
 // 连接Redis
 $redis = new Redis();
 $redis->connect('127.0.0.1', 6379);
 $redis->auth('123');
 echo 'Server is running '.$redis->ping();
```

## 四. PHP操作Redis的基本操作
``` php
 # 1. 实例化Redis
	$redis = new Redis();
```
```
 # 2. 连接Redis服务
	# (1) connect / open / pconnect / popen
	# 格式: $redis->connect(host, port [, timeout]);
	# host: 	@string 服务地址
	# port: 	@int    端口号
 	# timeout:	@float	连接时长 (可选, 默认为0, 不限连接时间)
	# 注: 在redis.conf中也有时间, 默认为300

	$redis->connect('127.0.0.1', 6379);
	# OR
	$redis->open('127.0.0.1', 6379);
	# OR pconnect / popen 不会主动关闭的连接
	$redis->pconnect('127.0.0.1', 6379);
	# OR
	$redis->popen('127.0.0.1', 6379);
```
```
 # 3. 设置Redis模式 setOption
	# don't serialize data
	$redis->setOption(Redis::OPT_SERIALIZER, Redis::SERIALIZER_NONE);
    # use built-in serialize/unserialize
	$redis->setOption(Redis::OPT_SERIALIZER, Redis::SERIALIZER_PHP);
    # use igBinary serialize/unserialize
	$redis->setOption(Redis::OPT_SERIALIZER, Redis::SERIALIZER_IGBINARY);
    # use custom prefix on all keys
	$redis->setOption(Redis::OPT_PREFIX, 'myAppName:');
```
```
 # 4. 查看redis设置的模式 getOption
	$redis->getOption(Redis::OPT_SERIALIZER);
```
```
 # 5. 查看连接状态 ping
	$redis->ping(); // +PONG
```
```
 # 6. 清空当前数据库 flushDB
	$redis->flushDB();
```
```
 # 7. 清空所有数据库 flushAll
	$redis->flushAll();
```
```
 # 8. 随机返回一个key randomKey
	$redis->randomKey();
```
```
 # 9. 选择一个数据库 select
	# 格式: $redis->select(N); N 为整数
	$redis->select(0);
```
```
 # 10. 转移一个Key到另一个数据库 move
	# 格式: $redis->move(key, N);
	$redis->move('test', 1);
```
```
 # 11. 重命名key rename / renameKey / renameNx(不重名)
	# 格式: $redis->rename(key, newKey);
	# 格式: $redis->renameKey(key, newKey);
	$redis->rename('test', 'test1');
```
```
 # 12. 设定key的活动时间 (s) setTimeout / expire
	$redis->setTimeout('test1', 3);
```
```
 # 13. 设置key存活到时间戳时间 expireAt
	$redis->expireAt('test1', time()+3);
```
```
 # 14. 获取满足给定pattern的所有key keys/getKeys
	$redis->keys('test*');
```
```
 # 15. 查看数据库中的key数量 dbSize
	$redis->dbSize();
```
```
 # 16. 密码认证 auth
	$redis->auth('123');
```
```
 # 17. 使用aof持久化数据库 bgrewriteaof
	$redis->bgrewriteaof();
```
```
 # 18. 选择从服务器 slaveof
	# 格式: $redis->slaveof(host, port);
	$redis->slaveof('10.1.1.1', 6379);
```
```
 # 19. 将数据同步保存到磁盘 save
	$redis->save();
```
```
 # 20. 将数据异步保存到磁盘 bgsave
	$redis->bgsave();
```
```
 # 21. 获取上次成功保存数据库的Unix时间戳
	$redis->lastSave();
```
```
 # 22. 获取Redis的版本信息 info
	$redis->info();
```
```
 # 23. 获取可以的类型值 type
	# 格式:＄redis->type(key);
	# 注: string: Redis::REDIS_STRING
		# set: Redis::REDIS_SET
		# list: Redis::REDIS_LIST
		# zset: Redis::REDIS_ZSET
		# hash: Redis::REDIS_HASH
		# other: Redis::REDIS_NOT_FOUND
	$redis->type('test1');
	
```

Author [@Snaker95][1]

[1]: http://www.sharedsea.com


