---
title: Lumen引入Redis数据库
categories: PHP
tags: Laravel
keywords: [PHP,Laravel,lumen]
description: Lumen引入Redis数据库的注意事项和基本操作

---

[TOC]



# Lumen引入Redis数据库

Redis作为强大的NoSql数据库, 其处理速度极快, 常用来作为缓存服务; 

## Lumen 配置Redis

* 该配置一般在config/database.php中配置, 也可以通过.env进行配置; 
* 引入缓存系统, `use Illuminate\Support\Facades\Cache;`, 通过`Cache::store('redis')->get($key)`来选择使用的缓存系统; 此时选用了`Redis`作为缓存
* 测试使用`Redis`

## 常见错误

* *Type error: Argument 1 passed to Illuminate\Cache\RedisStore::__construct() must be an instance of Illuminate\Contracts\Redis\Factory, instance of Redis given, called in /data/community/vendor/illuminate/cache/CacheManager.php on line 199*
  * 解决方案: 在`bootstrap/app.php`中添加`$app->register(\Illuminate\Redis\RedisServiceProvider::class);`
* *Class 'Illuminate\Redis\RedisServiceProvider' not found*
  * 解决方案: 执行`composer require illuminate/redis`, 此时会引入predis/predis和illuminate/redis两个依赖包, 也可以分别单独引入
* Redis安装:
  * yum install Reids
* PHP Redis扩展
  * yum install php-pecl-redis

Author [@Snaker95][1]

[1]: http://www.sharedsea.com