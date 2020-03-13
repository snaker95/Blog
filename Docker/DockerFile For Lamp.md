---
title: Dockerfile - lamp
categories: Docker 
tags: Docker
keywords: [Docker, Dockerfile, lamp]
description: dockerfile for lamp

---

<!--more-->

[TOC]


# DockerFile For Lamp
　　随着docker技术越来越流行, 学会搭建自己的docker镜像也是很必要的, 现在就lamp环境,学习一下dockerfile文件的书写
## dockerfile的代码

```sh
    FROM centos:6.8
    MAINTAINER snaker95
    
    ADD ./config_mysql.sh /config_mysql.sh
    ADD ./start.sh /start.sh
    RUN chmod 755 /config_mysql.sh && chmod 755 /start.sh && /config_mysql.sh;rm -rf /config_mysql.sh
    
    EXPOSE 22 80 6379 3306 443
    
    CMD ["/bin/bash", "/start.sh"]
```

## 其他辅助文件
### 辅助文件一: config_mysql.sh

```sh
    #!/bin/bash
    __httpd_install() {
    	echo 'START to install httpd ...'
    	yum -y update && yum -y install epel-release
    	yum -y install httpd httpd-devel vim-enhanced bash-completion;yum clean all
    	sed -i 's/ServerName www.example.com:80/# ServerName www.example.com:80 \n ServerName localhost:80/g' /etc/httpd/conf/httpd.conf
    	sleep 10
    	service httpd start
    }
    __mysql_config() {
    # Hack to get MySQL up and running... I need to look into it more.
    echo "Running the mysql_config function."
    # yum -y erase mysql mysql-server
    # rm -rf /var/lib/mysql/ /etc/my.cnf
    # yum -y install mysql mysql-server
    
    yum -y install mysql55w mysql55w-server
    mysql_install_db
    chown -R mysql:mysql /var/lib/mysql
    /usr/bin/mysqld_safe &
    sleep 10
    }
    
    __start_mysql() {
    echo "Running the start_mysql function."
    mysql -uroot -e "GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'root123' WITH GRANT OPTION; FLUSH PRIVILEGES;"
    mysqladmin -u root password root123
    killall mysqld
    sleep 10
    }
    
    __php_install() {
    	# yum install -y php php-mysql php-devel php-gd php-pecl-memcache php-pspell php-snmp php-xmlrpc php-xml php-pecl-redis php-pecl-apc php-cli php-pear php-pdo php-mysql php-pecl-memcache php-gd php-mbstring php-mcrypt php-xml php-adodb php-imap php-intl php-soap php-mysqli php-zip php-iconv php-curl php-simplexml php-dom php-bcmath php-opcache --skip-broken;yum clean all
    	echo "START yum PHP ..."
    	yum -y --skip-broken install php56w*
    	sleep 10
    }
    
    __redis_install() {
    	yum -y install epel-release
    	yum -y install redis && yum clean all
    	sed -i 's/bind 127.0.0.1/# bind 127.0.0.1 \n bind 0.0.0.0/g' /etc/redis.conf
    }
    # Call all functions
    __httpd_install
    rpm -Uvh http://repo.webtatic.com/yum/el6/latest.rpm
    __mysql_config
    __start_mysql
    __php_install
    __redis_install
    yum -y remove epel-release
    yum clean all
```
### 辅助文件二: start.sh

```sh
    #!/bin/bash
    service httpd restart && \
    service mysqld restart && \
    service redis start && \
    cat /etc/issue && \
    httpd -v && \
    mysql --version && \
    php -v && \
    tail -f /start.sh
```

## 生成镜像

```sh
# 切换到dockerfile的目录下
root $ cd /docker
root $ docker build -t 用户名/镜像名称 .
```
Author [@Snaker95][1]

[1]: http://www.sharedsea.com


