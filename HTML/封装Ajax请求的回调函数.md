---
title: 封装Ajax请求的回调函数
categories: [JavaScript]
tags: HTML
keywords: [HTML,JS,Ajax]
description: JQuery中的Ajax请求非常好的, 既支持`get`也 支持`post`, 很方便; 但是如果页面中有很多Ajax请求, 需要书写多次Ajax格式, 实话是说 - 有点不方便

---

[TOC]

# 封装Ajax请求的回调函数
JQuery中的Ajax请求非常好的, 既支持`get` 也 支持 `post`, 很方便; 但是如果页面中有很多Ajax请求, 需要书写多次Ajax格式, 实话是说: 有点不方便, 所以, 借助`老王`思想,重新封装了新的方法.
<!--more-->
## 一. 源码
```js
$.send = (function() {
    linkstatus = true; // 全局变量, 防止重复重复提交
    var default_config = {
        'type'      :   'post'
        ,'url'       :   ''
        ,'data'      :   {}
        ,'dataType'  :   'json'
        ,'async'     :   true
    };
    return function( config , func ) {
        if( ! linkstatus ) {
            return false;
        }
        // 合并参数
        for(i in config) {
            default_config[i] = config[i];
        }
        try{
            $.ajax({
                type            :   default_config['type']
                ,url            :   default_config['url']
                ,data           :   default_config['data']
                ,dataType       :   default_config['dataType']
                ,async          :   default_config['async']
                ,beforeSend     :   function(e) {
                    linkstatus = false;
                    $('body').css('cursor', 'wait');
                }
                ,success        :   function(e) {
                    func(e); return;
                }
                ,complete       :   function() {
                    linkstatus = true;
                    $('body').css('cursor', 'default');
                }
            });
        }catch(e) {
            return false;
        }
    }

})();
```
**讲解:** 使用前必须引入`JQuery`;

## 二. 使用
```js
// 引入以上代码
var url = 'http://127.0.0.1/pc-jxgj/DbDrivers/driver'; //请求地址
var config = {
	'url': url 
}
$.send(config, function(e) {
    console.log(e);
});
```
Author [@Snaker95][1]

[1]: http://www.sharedsea.com

