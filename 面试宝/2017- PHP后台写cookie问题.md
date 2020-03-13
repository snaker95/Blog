---
title: PHP后台写cookie问题
categories: 面试 
tags: 面试
keywords: [面试, 基础知识, php, cookie]
description: PHP后台写cookie问题

---

<!--more-->

[TOC]

# PHP后台写cookie问题
  今天在面试过程中, 被问到一个这样的问题: 在php文件中使用函数`setcookie()`, 紧接着使用`$_COOKIE`读取缓存, 是否能读取到?
  答案: 能(错误)
      应该是不可以的, 因为cookie是通过客户端请求携带过来的, 后台使用`setcookie()`函数为客户端设置了COOKIE, 但是当前请求并没有该字段, 所以不会读取到, 只有在下一次请求时才会携带过来. 总的来说会晚一次