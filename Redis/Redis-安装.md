---
title: Redis-安装
categories: Redis   
tags: Redis
keywords: [Redis]
description: Redis在Window和Linux环境中的安装

---

[TOC]

# Redis-安装
Redis在Window和Linux环境中的安装步骤
<!--more-->
## 一. Window环境安装
> 1. 下载地址: [Window-Redis]( https://github.com/dmajkic/redis/downloads) `https://github.com/dmajkic/redis/downloads`
> 2. 运行方式: 在CMD中,
```
# 服务器端
$> redis-server.exe redis.conf

## 客户端
$> redis-cli.exe -h 127.0.0.1 -p 6379 
```

# 二. Linux环境安装
> 1. 下载地址: [Linux-Redis](http://redis.io/download)　或者　[Linux-Redis-2.8.17](http://download.redis.io/releases/redis-2.8.17.tar.gz)
> 2. 运行方式: 解压后,
```
[root ~] cd redis-xx.xx
[root redis] make install
[root redis] find / -name reids*
``` 
**讲解:** 进入Redis目录中, make install, 应该可以直接使用redis的相关命令了

Author [@Snaker95][1]

[1]: http://www.sharedsea.com


