---
title: PHP 汉英混排计算字符串长度
categories: PHP
tags: PHP
keywords: [PHP,字符串长度,汉英]
description: 在PHP后台做数据验证是必须的, 而限制字符串长度是常见不过的, 但是由于汉字在UTF-8编码中,长度却是3个, 所以,在汉英混排时计算字符串长度就不是很好确定了

---

[TOC]

# PHP 汉英混排计算字符串长度
在PHP后台做数据验证是必须的, 而限制字符串长度是常见不过的, 但是由于汉字在UTF-8编码中,长度却是3个, 所以,在汉英混排时计算字符串长度就不是很好确定了, 但是PHP中已提供了相关函数
<!--more-->
## 一. 函数介绍
> `strlen()` 和 `mb_srelen()`

```php
$str="中文a字1符";
echo strlen($str);
echo "<br />";
echo mb_strlen($str,"UTF8");
//输出结果
    //14
    //6
```

## 二. 计算字符串占位
> 中英混排占位 *(一个中文字符的占位是2，英文字符是1)*

```php
$str='中文a字1符';
//计算如下
echo ( strlen($str) + mb_strlen($str,'UTF8') ) / 2;
//输出结果
//10
```

## 三. 数据库中存储长度
> `varchar`、`char` 中文字符占位为1 

Author [@Snaker95][1]

[1]: http://www.sharedsea.com