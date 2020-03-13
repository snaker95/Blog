---
title: 变量作用域
categories: 面试 
tags: 面试
keywords: [面试, 基础知识, 变量]
description: 变量作用域

---

<!--more-->

[TOC]

# 变量作用域

## PHP函数变量作用域
  函数内读取全局变量,需要使用关键字"global",在函数中声明了global $a 之后，对该变量的引用都会指向其全局版本。

## Python函数变量作用域
  Python的作用域一共有4种:
  * **L**: (Local), 局部作用域
  * **E**: (Enclosing), 闭包函数外的函数中
  * **G**: (Global), 全局作用域
  * **B**: (Built-in), 内建作用域
      Python中的变量会以 L –> E –> G –>B 的规则查找，即：在局部找不到，便会去局部外的局部找（例如闭包），再找不到就会去全局找，再者去内建中找
  * Python中,想在函数内修改全局变量,需要使用关键字global

  **参考**: 
  [php与Python函数内读取局部变量的区别](http://www.cnblogs.com/ch459742906/p/6946269.html)