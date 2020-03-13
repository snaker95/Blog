---
title: Redis-keys命令
categories: Redis   
tags: Redis
keywords: [Redis,keys命令]
description: Redis的keys命令

---

[TOC]

# Redis-keys命令
  Redis 数据库是key-value类型存储方式, 支持的数据类型有 `字符串`(string)、 `哈希`(Hash)、 `列表`(list)、 `集合`(set)、 `有序集合`(Zset). 

  现在学习一下**keys相关命令**的相关操作
**1. 删除key**
``` sh
 del key
```

**2. 序列化key并返回**
```
 dump key
```

**3. 检测key是否存在**
```
 exists key
```

**4. 设置过期时间, 单位为秒**
```
 expire key seconds
```

**5. 设置过期时间, 为秒时间戳**
```
 expireat key timestamp
```

**6. 设置过期时间, 单位为毫秒**
```
 pexpire key milliseconds
```

**7. 设置过期时间, 为毫秒时间戳**
```
 pexpireat key milliseconds-timestamp
```

**8. 查找所有符合给定模式(pattern)的key**
```
 keys pattern  # pattern 类是正则
```

**9. 将key移动到指定数据库db**
```
 move key db
```

**10. 移除key的过期时间,持久保持**
```
 persist key
```

**11. 获取key的剩余的过期时间, 单位为毫秒**
```
 pttl key
```

**12. 获取key的生存时间, 单位为秒**
```
 ttl key
```

**13. 从数据库中随机返回一个key**
```
 randomkey
```

**14. 修改key的名称**
```
 rename key newkey
```

**15. 仅当newkey不存在,才能修改**
```
 renamenx key newkey
```

**16. 获取key存储值的类型**
```
 type key
```

Author [@Snaker95][1]

[1]: http://www.sharedsea.com


