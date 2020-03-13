---
title: MySQL修改字段、修改表索引等操作
categories: MySql
tags: MySql
keywords: [Mysql,基本操作]

---

[TOC]



# MySQL修改字段、修改表索引等操作

1. 展示一张已经存在的表的建表语句

   ```mysql
   show create table （目标表)
   ```

2. 将表引擎改为innodb  

   ```mysql
   alter table t_starmcn engine=innodb;
   ```

3. 修改表名

   ```mysql
   alter table old_name rename [to] new_name;
   ```

4. 修改表中字段的数据类型
   ```mysql
   alter table table_name modify 属性名 数据类型;
   ```

5. 修改表中字段的数据类型
   ```mysql
   alter table table_name modify 属性名 数据类型;
   ```

6. 增加字段
   ```mysql
   alter table table_name add 属性名1 数据类型 [完整性约束条件] [FIRST | after 属性名2];
   ```

7. 删除字段
   ```mysql
   alter table table_name drop 属性名；
   ```

8. 删除表的外键约束
   ```mysql 
   alter table table_name drop foreign key 外键名；
   ```

9. 删除没有被关联的普通表
   ```mysql
    drop table table_name；
   ```

10. 删除被其他表关联的父表

  > 先删除外键
  > 然后在使用drop table 表名语句

11. 创建索引 可以用alter也可以用create
   ```mysql
   alter table table_name add index index_name 列名;
   alter table table_name add unique 列名;
   alter table table_name add primary key 列名;
   -- [注释] 多列时各列之间用逗号分隔。索引名index_name可选，缺省时，MySQL将根据第一个索引列赋一个名称。另外，ALTER TABLE允许在单个语句中更改多个表，因此可以同时创建多个索引。
   
   create index index_name on table_name 列名;
   create unique index index_name on table_name 列名;
   ```


12. 删除索引
   ```mysql
   drop index index_name on table_name ;
   alter table table_name drop index index_name ;
   alter table table_name drop primary key ;
   ```

13. 修改索引
   ```mysql
   --先删除
   ALTER TABLE table_name DROP INDEX index_name;
   
   --再以修改后的内容创建同名索引
   CREATE INDEX index_name ON table_name;
   ```
