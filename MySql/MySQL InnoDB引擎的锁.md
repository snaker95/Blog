---
title: MySQL InnoDB引擎的锁
categories: MySql
Tags: MySql
keywords: [Mysql,锁]
description: 关于MySQL中InnoDB引擎的相关锁

---

[TOC]

# MySQL InnoDB引擎的锁

## 1. 乐观锁和悲观锁 
### 1.1 乐观锁:
  乐观锁不是数据库自带的，需要我们自己去实现。
  乐观锁是指操作数据库时(更新操作)，想法很乐观，认为这次的操作不会导致冲突，在操作数据时，并不进行任何其他的特殊处理（也就是不加锁），而在进行更新后，再去判断是否有冲突了。
  
  通常实现是这样的：在表中的数据进行操作时(更新)，先给数据表加一个版本(version)字段，每操作一次，将那条记录的版本号加1。也就是先查询出那条记录，获取出version字段,如果要对那条记录进行操作(更新),则先判断此刻version的值是否与刚刚查询出来时的version的值相等，如果相等，则说明这段期间，没有其他程序对其进行操作，则可以执行更新，将version字段的值加1；如果更新时发现此刻的version值与刚刚获取出来的version的值不相等，则说明这段期间已经有其他程序对其进行操作了，则不进行更新操作。
  ```mysql
    # 通过id查询数据
    SELECT * FROM `tables` WHERE `id`=#{id}
    # 根据查询出的数据进行相关操作
    ...
    ...
    # 处理后, 更新数据库
    UPDATE `tables` SET `field` = 2,`version`=`version`+1 WHERE `id`= #{id} AND `version`=#{version}
  ```

### 1.2 悲观锁
  悲观锁就是在操作数据时，认为此操作会出现数据冲突，所以在进行每次操作时都要通过获取锁才能进行对相同数据的操作, 所以悲观锁需要耗费较多的时间.
  悲观锁是由数据库自己实现了的，要用的时候，我们直接调用数据库的相关语句就可以了.
  
#### 1.2.1 共享锁
  共享锁指的就是对于多个不同的事务，对同一个资源共享同一个锁. 以MySQL为例:
  ```mysql
    SELECT * FROM table_name WHERE ... LOCK IN SHARE MODE;
  ```
#### 1.2.2 排他锁
  对于多个不同的事务，对同一个资源只能有一把锁。
  ```mysql
    SELECT * FROM table_name WHERE ... FOR UPDATE;
  ```
  > 详见: [InnoDB 行级锁](http://blog.csdn.net/zhll3377/article/details/8669216)

## 行锁

## 表锁