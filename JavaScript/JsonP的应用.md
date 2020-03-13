---
title: JsonP的应用
categories: JavaScript   
tags: JavaScript
keywords: [JavaScript, JS, JsonP]
description: Json是一个很好玩的东东, 但是有些限制, 不能跨域玩耍...

---
[TOC]

# JsonP的应用
Json是一个很好玩的东东, 但是有些限制, 不能跨域玩耍...,不过嘛,问题总有解决的方案, JsonP就能愉快的完成此任务
<!--more-->
## 一. JsonP的后台PHP程序
```php
public function index() {
    $result_data = array();
    if(!isset($_GET['jsonpCallback'])) {
        echo 'try{jsonpCallback('.json_encode($result_data).');}catch(e){}';
        exit;
    }else {
        echo 'try{'.$_GET['jsonpCallback'].'('.json_encode($result_data).');}catch(e){}';
        exit;
    }
}
```

## 二. JsonP的前端JS程序
```js
$.ajax({
    type: "post",
    url: "http://www.sharedsea.com/dd/index.php/site?jsonpCallback=BusuanziCallback",
    dataType: "jsonp",
    jsonp: "callback",
    jsonpCallback:"BusuanziCallback",
    success: function(json) {
        console.log(json);
    },
    error: function(json) {
        alert('error');
    }
});
```
**讲解:** `Ajax`中参数 `jsonpCallback`的值 与 `url` 中 `jsonpCallback`的值 保持一致

## 三. 总结
> 1. `url`   :   为PHP程序的url, 其中参数为 `jsonpCallback` 为PHP中的参数
> 2. `BusuanziCallback`  :  为参数的`VLAUE`值
 
Author [@Snaker95][1]

[1]: http://www.sharedsea.com


