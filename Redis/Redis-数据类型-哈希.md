---
title: Redis-数据类型-哈希
date: 2016-06-08 11:28:20
categories: Redis   
tags: Redis
keywords: [Redis,hash]
description: Redis的数据类型-Hash(哈希)

---

[TOC]

# Redis-数据类型-哈希
  Redis 数据库是key-value类型存储方式, 支持的数据类型有 `字符串`(string)、 `哈希`(Hash)、 `列表`(list)、 `集合`(set)、 `有序集合`(Zset). 

  现在学习一下**哈希**类型的相关操作
**1. 删除哈希表字段 `hdel`**
```
 HDEL key field1 [field2 ... ] 	# 删除key中的fields
```

**2. 查看字段是否存在 `hexists`**
```
 HEXISTS key field	# 哈希表key中是否存在field
```

**3. 获取哈希表中指定字段的值**
```
 HGET key field  # 获取哈希表中指定字段field
```

**4. 获取哈希表中指定key的所有字段和值**
```
 HGETALL key  # 获取哈希表中指定key所有字段和值
```

**5. 哈希表中指定key中指定字段的整数新增N `hincrby`**
```
 HINCRBY key field N  # 在field值上新增N
```

**6. 哈希表中指定key中指定字段的浮点数新增N.M `hincrbyfloat`**
```
 HINCRBYFLOAT key field N.M  # 在field值上新增N.M
```

**7. 获取哈希表中key的所有字段 `hkeys`**
```
 HKEYS key  # 获取key中所有字段fields
```

**8. 获取哈希表中字段长度 `hlen`**
```
 HLEN key  # 获取哈希中字段fields的长度
```

**9. 获取哈希表中字段的值 `hmget`**
```
 HMGET key field [field1 ... ]  # 获取key中fields的值
```

**10. 设置哈希表中多个字段和值 `hmset`**
```
 HMSET key field value [field1 value1 ... ]
```

**11. 设置哈希表中单个字段和值 `hset`**
```
 HSET key field value
```

**12. 只有字段不存在才可设置 `hsetnx`**
```
 HSETNX key field value  # 只有field不存在才可设置 
```

**13. 获取哈希表中的所有值 `hvals`**
```
 HVALS key  # 获取key 所有值
```

**14. 迭代哈希表中的键值对 `hscan`**
```
 HSCAN key cursor [MATCH pattern] [COUNT count]
```

Author [@Snaker95][1]

[1]: http://www.sharedsea.com


