# protobuf 简介

[TOC]

Google Protocol Buffer( 简称 Protobuf) 是 Google 公司内部的混合语言数据标准, 用于 RPC 系统和持续数据存储系统。

Protocol Buffers 是一种轻便高效的结构化数据存储格式，可以用于结构化数据串行化，或者说序列化。它很适合做数据存储或 RPC 数据交换格式。可用于通讯协议、数据存储等领域的语言无关、平台无关、可扩展的序列化结构数据格式。

## 安装 protobuf

在网站 http://code.google.com/p/protobuf/downloads/list上可以下载 Protobuf 的源代码。然后解压编译安装便可以使用它了。

安装步骤如下所示：

```shell
tar -xzf protobuf-2.1.0.tar.gz 
cd protobuf-2.1.0 
./configure --prefix=$INSTALL_DIR 
make 
make check 
make install
```

## 书写 protobuf

首先我们需要编写一个 proto 文件，定义我们程序中需要处理的结构化数据，在 protobuf 的术语中，结构化数据被称为 Message。proto 文件非常类似 java 或者 C 语言的数据定义。代码清单 1 显示了例子应用中的 proto 文件内容

清单 1

```protobuf
package lm;
message helloworld 
{ 
   required int32     id = 1;  // ID 
   required string    str = 2;  // str 
   optional int32     opt = 3;  //optional field 
}
```

一个比较好的习惯是认真对待 proto 文件的文件名。比如将命名规则定于如下：

> **packageName.MessageName.proto**

在上例中，package 名字叫做 lm，定义了一个消息 helloworld，该消息有三个成员，类型为 int32 的 id，另一个为类型为 string 的成员 str。opt 是一个可选的成员，即消息中可以不包含该成员。

## 编译 proto 文件

写好 proto 文件之后就可以用 Protobuf 编译器将该文件编译成目标语言了, 现在支持

| 语言 | 源代码 |
| ---- | ---- |
|  C++ | [src](https://github.com/google/protobuf/blob/master/src)|
| Java |  [java](https://github.com/google/protobuf/blob/master/java) |
| Python | [python](https://github.com/google/protobuf/blob/master/python)|
|Objective-C | [objectivec](https://github.com/google/protobuf/blob/master/objectivec)|
| C# | [csharp](https://github.com/google/protobuf/blob/master/csharp) |
| JavaNano | [javanano](https://github.com/google/protobuf/blob/master/javanano)|
| JavaScript |  [js](https://github.com/google/protobuf/blob/master/js)  |
| Ruby | [ruby](https://github.com/google/protobuf/blob/master/ruby) |
| Go|  [golang/protobuf](https://github.com/golang/protobuf)  |
| PHP |[php](https://github.com/google/protobuf/blob/master/php)  |
| Dart| [dart-lang/protobuf](https://github.com/dart-lang/protobuf)|

## Varint 讲解

Varint 是一种紧凑的表示数字的方法。它用一个或多个字节来表示一个数字，值越小的数字使用越少的字节数。这能减少用来表示数字的字节数。

比如对于 int32 类型的数字，一般需要 4 个 byte 来表示。但是采用 Varint，对于很小的 int32 类型的数字，则可以用 1 个 byte 来表示。当然凡事都有好的也有不好的一面，采用 Varint 表示法，大的数字则需要 5 个 byte 来表示。从统计的角度来说，一般不会所有的消息中的数字都是大数，因此大多数情况下，采用 Varint 后，可以用更少的字节数来表示数字信息。下面就详细介绍一下 Varint。

Varint 中的每个 byte 的最高位 bit 有特殊的含义，如果该位为 1，表示后续的 byte 也是该数字的一部分，如果该位为 0，则最后一个 byte。其他的 7 个 bit 都用来表示数字。因此小于 128 的数字都可以用一个 byte 表示。大于 128 的数字，比如 300，会用两个字节来表示：1010 1100 0000 0010

下图演示了 Google Protocol Buffer 如何解析两个 bytes。注意到最终计算前将两个 byte 的位置相互交换过一次，这是因为 Google Protocol Buffer 字节序采用 little-endian 的方式。

![图 6. Varint 编码](https://www.ibm.com/developerworks/cn/linux/l-cn-gpb/image006.jpg)

