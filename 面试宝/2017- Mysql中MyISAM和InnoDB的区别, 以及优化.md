---
title: MyIsam和InnoDB存储引擎的异同
categories: 面试 
tags: 面试
keywords: [面试, 基础知识, Mysql, MyIsam, InnoDB]
description: MyIsam和InnoDB储存引擎的异同

---

<!--more-->

[TOC]

# MyIsam和InnoDB存储引擎的异同
  MySQL数据库作为开源项目, 深受中小型企业和开发人员的喜爱, 因此MySQL的社区也非常活跃; 
  MySQL有多种存储引擎, 其中MyISAM和InnoDB是最为常见的2种;

## 基本介绍
  * 在MySQL数据库版本v5.5之前, 其默认存储引擎为MyISAM, 之后改为默认为InnoDB;

## MyISAM和InnoDB的区别:
### 1. 存储结构
  * **MyISAM** : 在磁盘中存在3个文件, 以表名`table name`开始, 扩展名指出文件类型, *.frm* : 存放表结构的相关信息文件, *.MYD* : (MYData), 存放数据文件, *MYI* : (MYIndex), 存放索引文件
  * **InnoDB** : 所有的表都保存在同一个数据文件中（也可能是多个文件，或者是独立的表空间文件），InnoDB表的大小只受限于操作系统文件的大小，一般为2GB

### 2. 存储空间
  * **MyISAM** : 可被压缩，存储空间较小。支持三种不同的存储格式：静态表(默认，但是注意数据末尾不能有空格，会被去掉)、动态表、压缩表;
  * * 静态表: 如果数据表中的各数据列的长度都是预先固定好的，服务器将自动选择这种表类型。因为数据表中每一条记录所占用的空间都是一样的，所以这种表存取和更新的效率非常高。当数据受损时，恢复工作也比较容易做
  * * 动态表: 如果数据表中出现varchar、xxxtext或xxxBLOB字段时，服务器将自动选择这种表类型。相对于静态MyISAM，这种表存储空间比较小，但由于每条记录的长度不一，所以多次修改数据后，数据表中的数据就可能离散的存储在内存中，进而导致执行效率下降。同时，内存中也可能会出现很多碎片。因此，这种类型的表要经常用optimize table 命令或优化工具来进行碎片整理
  * * 压缩表: 以上说到的两种类型的表都可以用myisamchk工具压缩。这种类型的表进一步减小了占用的存储，但是这种表压缩之后不能再被修改。另外，因为是压缩数据，所以这种表在读取的时候要先时行解压缩。
  * **InnoDB** : 需要更多的内存和存储，它会在主内存中建立其专用的缓冲池用于高速缓冲数据和索引

### 3. 可移植性、备份及恢复
 * **MyISAM** : 数据是以文件的形式存储，所以在跨平台的数据转移中会很方便。在备份和恢复时可单独针对某个表进行操作。
 * **InnoDB** : 免费的方案可以是拷贝数据文件、备份 binlog，或者用 mysqldump，在数据量达到几十G的时候就相对痛苦了。

### 4. 事务支持
  * **MyISAM** : 强调的是性能，每次查询具有原子性,其执行数度比InnoDB类型更快，但是不提供事务支持。
  * **InnoDB** : 提供事务支持事务，外部键等高级数据库功能。 具有事务(commit)、回滚(rollback)和崩溃修复能力(crash recovery capabilities)的事务安全(transaction-safe (ACID compliant))型表。

### 5. AUTO_INCREMENT
  * **MyISAM** : 可以和其他字段一起建立联合索引。引擎的自动增长列必须是索引，如果是组合索引，自动增长可以不是第一列，他可以根据前面几列进行排序后递增
  * **InnoDB** : Inno表锁差异DB中必须包含只有该字段的索引。引擎的自动增长列必须是索引，如果是组合索引也必须是组合索引的第一列。

### 6. 表锁差异
  * **MyISAM** : 只支持表级锁，用户在操作myisam表时，select，update，delete，insert语句都会给表自动加锁，如果加锁以后的表满足insert并发的情况下，可以在表的尾部插入新的数据
  * **InnoDB** : 支持事务和行级锁，是innodb的最大特色。行锁大幅度提高了多用户并发操作的新能。但是InnoDB的行锁，只是在WHERE的主键是有效的，非主键的WHERE都会锁全表的。

### 7. 全文索引
  * **MyISAM** : 支持 FULLTEXT类型的全文索引
  * **InnoDB** : 不支持FULLTEXT类型的全文索引，但是innodb可以使用sphinx插件支持全文索引，并且效果更好。

### 8. 表主键
  * **MyISAM** : 允许没有任何索引和主键的表存在，索引都是保存行的地址。
  * **InnoDB** : 如果没有设定主键或者非空唯一索引，就会自动生成一个6字节的主键(用户不可见)，数据是主索引的一部分，附加索引保存的是主索引的值。

### 9. 表的具体行数
  * **MyISAM** : 保存有表的总行数，如果`select count(*) from table;`会直接取出出该值
  * **InnoDB** : 没有保存表的总行数，如果使用`select count(*) from table；`就会遍历整个表，消耗相当大，但是在加了wehre条件后，myisam和innodb处理的方式都一样。

### 10. CURD操作
  * **MyISAM** : 如果执行大量的SELECT，MyISAM是更好的选择。
  * **InnoDB** : 如果数据执行大量的INSERT或UPDATE，出于性能方面的考虑，应该使用InnoDB表。DELETE 从性能上InnoDB更优，但DELETE FROM table时，InnoDB不会重新建立表，而是一行一行的删除，在innodb上如果要清空保存有大量数据的表，最好使用truncate table这个命令。

### 11. 表的具体行数
  * **MyISAM** : 外键
  * **InnoDB** : 支持

## 总结:
  通过上述的分析，基本上可以考虑使用InnoDB来替代MyISAM引擎了，原因是InnoDB自身很多良好的特点，比如事务支持、存储 过程、视图、行级锁定等等，在并发很多的情况下，相信InnoDB的表现肯定要比MyISAM强很多。另外，任何一种表都不是万能的，只用恰当的针对业务类型来选择合适的表类型，才能最大的发挥MySQL的性能优势。如果不是很复杂的Web应用，非关键应用，还是可以继续考虑MyISAM的，这个具体情况可以自己斟酌。
  
  **参考**:
  [MySQL存储引擎中的MyISAM和InnoDB区别详解](http://www.jb51.net/article/62457.htm)
  [mysql表类型MyISAM和InnoDB有什么区别？](http://lib.csdn.net/article/mysql/4123)



