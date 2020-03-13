---
title: MySql 有数据添加从数据库
categories: MySql
Tags: MySql
keywords: [Mysql,主从服务器]
description: MySql主从服务器搭建,从而提高效率

---

[TOC]



# 新增从库

人会犯错, 机器也会犯傻, 工作中的不顺利也会犯难, 最近公司数据库(从)突然崩了, 原因是mysql自带库全部悄悄消失, 那个mysql永远的起不来了, 也没有找到相关备份; 最终选择重新安装并重新挂载到master上;

## 最开始

本以为安装并启动mysql后设置slave就ok, 可是真的不是这样, 因为确定不了Position, master的Position一直在变化, 启动新的slave也不能正常同步; 

## 度娘谷爹时代

从度娘和谷爹上找了些资料, 很多说新增`从库`都会从主库或者`其他从库`备份数据出来, 然后找到对应的 mysql-bin文件和Position位置, 然后开始同步

## 最后选择

最后选择了比较流行的工具`xtrabackup` 

安装过程就略过了

...

使用xtrabackup 备份和恢复数据过程:

### xtrabackup 备份

 1. 在主服务器进行热备份, (不采用mysqldump 是因为会锁表, 数据量太大时间太长)

    ```shell
    # 选择mysql的配置文件, 用户名, 密码 和备份路径
    innobackupex --defaults-file=/usr/local/mysql/my.cnf --user=root --password=A2sW13  /ptbak/
    ```

	2. 备份完成后, 需要将binlog应用到数据文件中 (**必须做**)

    ```shell
    # 关键是 --apply-log
    innobackupex --defaults-file=/usr/local/mysql/my.cnf --user=root --password=A2sW13 --apply-log /ptbak/2018-02-06_03-49-02/
    ```

### 迁移备份数据

使用各自擅长的方法, 把备份好的数据迁移到从库服务器上

### 恢复数据准备

1. 停掉目标服务器(从库服务器)的mysql进程
2. 备份原数据目录 (以防万一)
3. 清空原数据目录, 为恢复备份做好铺垫 

### 正式恢复数据

1. 恢复数据

   ```shell
   # 重点 --copy-back 数据目录, 日志到my.cnf规定的目录中
   innobackupex --defaults-file=/etc/my.cnf --user=root --copy-back /root/2018-02-06_03-49-02/
   ```

2. 修改数据目录权限

   ```shell
   # 启动mysql服务的用户和组
   chown -R mysql:mysql /usr/local/mysql/data
   ```

3. 删除数据目录下的ib_logfile, 否则mysql启动可能报错

   ```shell
   rm -rf ib_logfile*
   ```

4. 启动mysql (各有各的启动脚本或命令)

5. 连接mysql数据库, 用户名和密码与源数据库相同, 可改可不改

6. 查看备份的一致性信息 (其实就是找mysql-bin和postion)

   ```shell
   # 备份文件中查找
   cat /root/2018-02-06_03-49-02/xtrabackup_binlog_info 
   mysql-bin.000969	307486338
   ```

7. 找到主库用于主从同步使用的用户和密码, 也可重建

   ```mysql
   GRANT REPLICATION SLAVE ON *.* TO 'repl'@'192.168.80.12' identified by '123456';
   ```

8. 启动从库的复制进程
