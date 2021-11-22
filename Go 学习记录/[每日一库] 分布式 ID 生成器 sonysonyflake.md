# [每日一库] 分布式 ID 生成器 sony/sonyflake

### git地址

https://github.com/sony/sonyflake

* 39 bits for time in units of 10 msec
* 8 bits for a sequence number
* 16 bits for a machine id

```
+--------------------------------------------------------------------------+
| 1 Bit Unused | 39 Bit Timestamp |  8 Bit NodeID  |   16 Bit Sequence ID |
+--------------------------------------------------------------------------+
```

* 时间戳只用了 39 位, 但是时间单位为 10 毫秒, 故比 41 位时间戳还用长 174 年

