# [每日一库] 分布式 ID 生成器 bwmarrin/snowflake

在集群横流的年代里, 分布式成为王道, 解决分布式的重点是如何解决全局唯一, 正确的接入实例

可阅读https://zhuanlan.zhihu.com/p/359902466



## 分布式 ID 生成器

雪花算法（Snowflake）是twitter公司内部分布式项目采用的ID生成算法

#### git地址:



Snowflake ID组成结构：正数位（占1比特）+ 时间戳（占41比特）+ 机器ID（占5比特）+ 数据中心（占5比特）+ 自增值（占12比特），总共64比特组成的一个Long类型。

```
+--------------------------------------------------------------------------+
| 1 Bit Unused | 41 Bit Timestamp |  10 Bit NodeID  |   12 Bit Sequence ID |
+--------------------------------------------------------------------------+
```



#### 源码:

```go
func (n *Node) Generate() ID {

	n.mu.Lock()

	now := time.Since(n.epoch).Nanoseconds() / 1000000

  // 获取 now 和 step
	if now == n.time {
		n.step = (n.step + 1) & n.stepMask // 获取下一次序列, 并防止溢出

		if n.step == 0 {
			for now <= n.time {
				now = time.Since(n.epoch).Nanoseconds() / 1000000
			}
		}
	} else {
		n.step = 0
	}

	n.time = now

  // 计算最终的 id 值
	r := ID((now)<<n.timeShift |
		(n.node << n.nodeShift) |
		(n.step),
	)

	n.mu.Unlock()
	return r
}
```

```
 1  |                    41                        |     10      |     12      
    
   0|0001100 10100010 10111110 10001001 01011100 00|             |              // now 相当于移动了 10+12
   0|                                              |10 0011 1001 |              // node 相当于移动了 12
or 0|                                              |             |‭0000 00000000‬ // sequence 
------------------------------------------------------------------------------------------
   0|0001100 10100010 10111110 10001001 01011100 00|10001|1 1001|‭0000 00000000‬ //结果：910499571847892992
```

可参考: https://segmentfault.com/a/1190000011282426

#### 使用:

```go
package main

/*
@Time : 2021/4/28 下午10:55
@Author : snaker95
@File : main.go
@Software: GoLand
*/

import (
	"fmt"
	"github.com/bwmarrin/snowflake"
)

func main() {
	node, err := snowflake.NewNode(64)
	if err != nil {
		fmt.Println(err)
		return
	}
	id := node.Generate()
	fmt.Println("id", id)
	fmt.Println(id.Base2())
	fmt.Println(id.Base32())
	fmt.Println(id.Base64())
	fmt.Println(id.Int64())
	fmt.Println(id.String())
	fmt.Println(id.MarshalJSON())
	fmt.Println(
		"node:", id.Node(),
		"step:", id.Step(),
		"time:", id.Time(),
		)

	id = node.Generate()
	fmt.Println("id", id)
	fmt.Println(id.Base2())
	fmt.Println(id.Base32())
	fmt.Println(id.Base64())
	fmt.Println(id.Int64())
	fmt.Println(id.String())
	fmt.Println(id.MarshalJSON())
	fmt.Println(
		"node:", id.Node(),
		"step:", id.Step(),
		"time:", id.Time(),
	)
}
```



### 扩展

#### 位掩码

位掩码（BitMask），是”位（Bit）“和”掩码（Mask）“的组合词。”位“指代着二进制数据当中的二进制位，而”掩码“指的是一串用于与目标数据进行按位操作的二进制数字。组合起来，就是”用一串二进制数字（掩码）去操作另一串二进制数字“的意思。

主要用于位运算时通过&运算确保改位掩码对应的位存在意义

**例如**: 子网掩码: 255.255.255.0

​    二进制:  1111 1111 . 1111 1111 . 1111 1111 . 0000 0000      255.255.255.0

**&**     某 IP: 1100 0000 . 1010 1000 . 0001 1110 . 0000 1000      192.168.30.8

----------------------------------------------------------------------------------------------------------

网路地址:  1100 0000 . 1010 1000 . 0001 1110 . 0000 0000     192.168.30.0



**例如:** linux 系统中读写执行权限 rwx; 文件权限: 5

r 掩码:  0100

w掩码: 0010

x掩码: 0001

二进制: 0101

可读性: 0101 & 0100 = 0100   => 可读

可写性: 0101 & 0010 = 0000 => 不可读

可执行: 0101 & 0001 = 0001 => 可执行

读写性: (0101 & 0100) | (0101 & 0010) = 0100 => 可读不写

读可执行: (0101 & 0100) | (0101 & 0001) = 0101 => 可读可执行