---
title: PHP HTTP请求格式的判断
date: 2016-06-01 08:34:35
categories: PHP
tags: PHP
keywords: [PHP,post,get,put,delete,ajax]
description: 在TP框架中,有很多判断请求的方法,例如:IS_AJAX()等..

---

[TOC]

# PHP HTTP请求格式的判断
在TP框架中, 有很多判断请求的方法, 例如: IS_AJAX()等.., 可是在其他框架中就不一定有这样的方法了.
<!--more-->

```php
define('NOW_TIME',  $_SERVER['REQUEST_TIME']);
define('REQUEST_METHOD', $_SERVER['REQUEST_METHOD']);
define('IS_GET',    REQUEST_METHOD =='GET' ? true : false);
define('IS_POST',   REQUEST_METHOD =='POST' ? true : false);
define('IS_PUT',    REQUEST_METHOD =='PUT' ? true : false);
define('IS_DELETE', REQUEST_METHOD =='DELETE' ? true : false);
define('IS_AJAX',  ((isset($_SERVER['HTTP_X_REQUESTED_WITH']) && 
                     strtolower($_SERVER['HTTP_X_REQUESTED_WITH']) == 'xmlhttprequest') ||
                     !empty($_POST[C('VAR_AJAX_SUBMIT')]) ||
                     !empty($_GET[C('VAR_AJAX_SUBMIT')])
                   ) ? true : false);
```

**讲解:** 需要把这些常量放在入口文件中

Author [@Snaker95][1]

[1]: http://www.sharedsea.com