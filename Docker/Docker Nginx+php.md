# Docker 下搭建 Nginx 和 php 环境

[TOC]

## 搭建 PHP容器

```shell
docker run --name php-fpm5.6 -p 9000:9000 -v ~/Develop/WebServer/php:/var/www/html -d php:5.6-fpm
```

## 搭建 Nginx并 链接 PHP容器

```shell
docker run --name nginx -v ~/Develop/WebServer:/usr/share/nginx/html -v ~/Develop/Docker/nginx/conf/nginx.conf:/etc/nginx/nginx.conf -v ~/Develop/Docker/nginx/conf/conf.d:/etc/nginx/conf.d --restart=on-failure:10 --link php-fpm5.6 -p 80:80 -d nginx
```

## 配置 Nginx

* 基本配置不多讲, 注意的是 php 的代理模块

* ```conf
  set $PHPFPM_DOC_ROOT /var/www/html;
  location ~ \.php {
  fastcgi_pass   php-fpm5.6:9000;
  fastcgi_index  index.php;
  fastcgi_split_path_info ^((?U).+\.php)(/?.+)$;
  fastcgi_param  SCRIPT_FILENAME  $PHPFPM_DOC_ROOT$fastcgi_script_name;
  fastcgi_param PATH_INFO $fastcgi_path_info;
  fastcgi_param PATH_TRANSLATED $PHPFPM_DOC_ROOT$fastcgi_path_info;
  include        fastcgi_params;
  }
  ```

* 对于 `SCRIPT_FILENAME` 是有 php容器的工作目录 决定, 所以在这里有两种方法

  * 方案一: 修改nginx配置文件, 将SCRIPT_FILENAME 指定到 php 容器的工作目录下, 即:  `/var/www/html`
  * 方案二: 就是修改 php 容器的工作目录, 这个时候就需要在创建容器的时候修改 (不推荐)



## 感想

其实在对于项目来说, 打造项目的 dockerFile才是重中之重