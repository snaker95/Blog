---
title: Nginx + PHP-FPM 运行原理
categories: 面试 
tags: 面试
keywords: [面试, 基础知识, nginx, php, PHP-FPM]
description: Nginx + PHP-FPM 运行原理

---

<!--more-->

[TOC]

# Nginx + PHP-FPM 运行原理

## 一、代理和反向代理
  * 代理, 也就是正向代理, 它隐藏了真实的客户端请求, 客户端请求的服务会被代理服务器代替来请求, 某些科(fan)学(qiang)上网就是典型的正向代理;
  * 反向代理, 它隐藏了真实的服务端, 此时反向代理服务器把请求转发到真实服务器那里, 真实服务器处理并返回资源给反向代理服务器, 反向代理服务器转发资源给客户端. Nginx就是非常好的反向代理服务器, 用来做代理服务器.

## 二、PHP-FPM是什么
  1. CGI、FastCGI
  * CGI(COMMON GATEWAY INTERFACE)公共网关接口, 是 Web Server 与后台语言交互的协议;开发者可以使用任何语言处理Web Server 发来的请求，动态的生成内容。
  * 缺点: 每处理一个请求都需要 fork 一个全新的进程; 这样会非常低效; 过多的进程耗费资源和内存
  * FastCGI是CGI的升级, 允许在一个进程内处理多个请求, 提高了性能

  2. PHP-FPM (PHP FastCGI Process Manager)是PHP FastCGI的管理器, 包含了master(`PHP-FPM`)和worker(`php-cgi`)两种进程: master进程仅有一个, 负责监听端口, 接收web server的请求; worker进程为N个, 每worker进程都内嵌一个PHP解析器;
  * 处理流程:
  * * FPM 的 master 进程接收到请求
  * * master 进程根据配置指派特定的 worker 进程进行请求处理，如果没有可用进程，返回错误，这也是我们配合 Nginx 遇到502错误比较多的原因。
  * * worker 进程处理请求，如果超时，返回504错误
  * * 请求处理结束，返回结果

## 三、Nginx + PHP-FPM工作原理
Nginx 不仅仅是一个 Web 服务器，也是一个功能强大的 Proxy 服务器，除了进行 http 请求的代理，也可以进行许多其他协议请求的代理，包括本文与 fpm 相关的 fastcgi 协议。为了能够使 Nginx 理解 fastcgi 协议，Nginx 提供了 fastcgi 模块来将 http 请求映射为对应的 fastcgi 请求。

  * Nginx 的 fastcgi 模块提供了 fastcgi_param 指令来主要处理这些映射关系, 主要完成的工作是将 Nginx 中的变量翻译成 PHP 中能够理解的变量
  * fastcgi_pass指令用于指定 fpm 进程监听的地址，Nginx 会把所有的 `php` 请求翻译成 fastcgi 请求之后再发送到这个地址
  * ![20170404151830456](/Users/snaker95/Develop/Snaker95-Blog/面试宝/assets/20170404151830456.png)

  **参考**:
  [Nginx+Php-fpm运行原理详解](http://blog.csdn.net/u013474436/article/details/52972699)
  [深入理解PHP之：Nginx 与 FPM 的工作机制](http://blog.jobbole.com/99314/)