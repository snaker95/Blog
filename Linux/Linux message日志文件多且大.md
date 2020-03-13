# Linux 中message日志文件大且多

​	/var/log/messages – 包含整个系统的信息，包括系统启动期间被记录的日志。mail, cron, daemon, kern, auth等相关的日志信息在这里记录。

### 方法一: 

```shell
grep "/var/log/messages" /etc/rsyslog.conf
```

输出如下: 
$ grep “/var/log/messages” /etc/rsyslog.conf 
*.info;mail.none;authpriv.none;cron.none /var/log/messages

上述的输出中：

*.info 表明所有带有 INFO 标志的日志信息会被记录。 
mail.none,authpriv.none,cron.none 表明这三种错误信息不会在messages。 
你也可以指定 *.none，这样将没有任何日志信息被记录

我们这里就设置为*.none即可



### 方法二:

​    linux系统中/var/log/目录日志默认保存四周并不生成系统日志（syslog.log），当用户有规定保存多少天时才去进行修改。系统日志路径/var/log/目录下

logrotate是一个日志管理程序，用来把旧的日志文件删除（备份），并创建新的日志文件，这个过程称为“转储”。

#### 修改方法

1、添加系统日志文件
默认RedHat Linux不生成该日志文件，但可以配置/etc/syslog.conf让系统生成该日志文件。它和/etc/log/messages日志文件不同，它只记录警告信息，常常是系统出问题的信息，所以更应该关注该文件。要让系统生成该日志文件，在/etc/syslog.conf文件中加上：*.warning /var/log/syslog
2、修改系统日志文件保存的时间
修改/etc/logrotate.conf 中的

```shell
# keep 4 weeks worth of backlogs
rotate 4
# 改为rotate 12    /最多转储12次
# 将
/var/log/wtmp {
    monthly     #每次转储以月为周期，保留最近12个月的日志
    create 0664 root utmp   # 转储后文件不存在时创建它，文件所有者为root，　　所属组为utmp，对应的权限为0664
    rotate 1 # 中的1改为3，
}
```

保存后通过`service syslog restart`命令重启syslog进程。

`service rsyslog  restart`



<https://blog.csdn.net/weiguang1017/article/details/52438884>