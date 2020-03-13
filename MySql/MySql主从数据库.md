---
title: MySql主从数据库
categories: MySql
Tags: MySql
keywords: [Mysql,主从服务器]
description: MySql主从服务器搭建,从而提高效率

---

[TOC]

# MySql主从数据库

随着数据访问量逐渐增大,数据库的承受力也达到了最大值,为了提交效率,数据库使用主从复制、读写分离的技术进行解决.
怎么安装MySql数据库,在这里就不多说了,只说主从赋值:
## 1. 主从服务器分别做以下操作
> 1.1 版本一致
> 1.2 初始化表,并在后台启动MySql
> 1.3 修改root的密码

## 2. 修改主服务器master

```sh
$ vi  /etc/my.cnf
	[mysqld]
	log-bin=mysql-bin	// [必须]启用二进制日志
	server-id=222 		// [必须]服务器唯一ID，默认是1，一般取IP最后一段
```

## 3. 修改从服务器slave

```sh
$ vi  /etc/my.cnf
	[mysqld]
	log-bin=mysql-bin	// [不是必须]启用二进制日志
	server-id=226 		// [必须]服务器唯一ID，默认是1，一般取IP最后一段
```

## 4. 重启2台服务器的MySql

```sh
$ server mysqld restart
```

或者

```sh
$ /etc/init.d/mysql restart
```

## 5. 在主服务器上建立账户并授权slave

```sh
$ /usr/local/mysql/bin/mysql -uroot -p

$ mysql> GRANT REPLICATION SLAVE ON *.* to 'mysync'@'%' identified by 'q123456'; 
```

*__注__: 一般不用root帐号，&ldquo;%&rdquo;表示所有客户端都可能连，只要帐号，密码正确，此处可用具体客户端IP代替，如192.168.145.226，加强安全。*

## 6. 登录主服务器的mysql，查询master的状态

```text
$ mysql> show master status;
   +------------------+----------+--------------+------------------+
   | File             | Position | Binlog_Do_DB | Binlog_Ignore_DB |
   +------------------+----------+--------------+------------------+
   | mysql-bin.000004 |      308 |              |                  |
   +------------------+----------+--------------+------------------+
   1 row in set (0.00 sec)
```

*__注__：执行完此步骤后不要再操作 `主` 服务器MYSQL，防止主服务器状态值变化*

## 7. 配置从服务器Slave

```text
$ mysql>change master to master_host='192.168.145.222',master_user='mysync',
	master_password='q123456',master_log_file='mysql-bin.000004',
	master_log_pos=308;
$ mysql>start slave;    //启动从服务器复制功能
```

* __讲解__: `master_log_file` 为 `步骤 6.` 中的`File`, `master_log_pos` 为 `步骤 6.` 中的`Position`
*__注__：注意不要断开，308数字前后无单引号。*

## 8. 检查从服务器复制功能状态

```text
$ mysql>show slave status\G;
*************************** 1. row ***************************

          Slave_IO_State: Waiting for master to send event
          Master_Host: 192.168.2.222  //主服务器地址
          Master_User: mysync   //授权帐户名，尽量避免使用root
          Master_Port: 3306    //数据库端口，部分版本没有此行
          Connect_Retry: 60
          Master_Log_File: mysql-bin.000004
          Read_Master_Log_Pos: 600 //同步读取二进制日志的位置，
				  //大于等于Exec_Master_Log_Pos
          Relay_Log_File: ddte-relay-bin.000003
          Relay_Log_Pos: 251
          Relay_Master_Log_File: mysql-bin.000004
          Slave_IO_Running: Yes    //此状态必须YES
          Slave_SQL_Running: Yes     //此状态必须YES
                ......
```

*__注__：Slave_IO及Slave_SQL进程必须正常运行，即YES状态，否则都是错误的状态(如：其中一个NO均属错误)。*

-----
以上操作过程，主从服务器配置完成。

## 9. 主从服务器测试
> #### 测试步骤:
> > 1. 在主服务器MySql,建立数据库,并且在数据库中插入数据
> > 2. 在从服务器MySql查看

Author [@Snaker95][1]

[1]: http://www.sharedsea.com