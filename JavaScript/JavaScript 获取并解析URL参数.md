---
title: JavaScript 获取并解析URL参数
categories: [JavaScript]
tags: JavaScript
keywords: [HTML,JS,解析URL]
description: 在一些情况下, `js`部分需要通过URL的参数来改变页面的效果或者执行不同的方法, 获取URL参数就很必要了;

---

[TOC]

# JavaScript 获取并解析URL参数
在一些情况下, `js`部分需要通过URL的参数来改变页面的效果或者执行不同的方法, 获取URL参数就很必要了;
```js
/**
 * 解析URL参数
 * @param search string '?key=value&key2=value2'
 * @return new_search array []
 **/
function url_param(search) {
    if( ! search )  return false;
    // 去问号
    search = search.split('?')[1].split('&');
    var se_len = search.length;
    var new_search = [];
    for (var i = se_len - 1; i >= 0; i--) {
        var one = search[i].split('=');
        new_search[one[0]] = one[1];
    }
    return new_search;
}

/**
 * 调用URL解析
 **/
 var search = url_param(location.search)
 if(search && parseInt( search['category']) > 0) {
    $('select[name="category-picker"]').val(parseInt( search['category'])).change();
}
```
Author [@Snaker95][1]

[1]: http://www.sharedsea.com





