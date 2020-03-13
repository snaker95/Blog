---
title: JS表单提交
categories: JavaScript
tags: HTML
keywords: [HTML,JS,表单提交]
description: 表单提交的方式从最简单`form`默认提交, 到`JS`提交表单, 避免页面跳转影响体验度...

---

[TOC]

# JS表单提交
表单提交的方式从最简单`form`默认提交, 到`JS`提交表单, 避免页面跳转影响体验度..., 以下介绍我的基本思路:
<!--more-->
## 1. HTML页面相关
```html
<form action="javascript:void(0)" pre-action="" methos="post">

</form>
```
**注:** `form`中`action`设置为`javascript:void(0)`, 是**阻止**默认表单提交, 而`pre-action`中为提交表单的**地址**或**接口** 

## 2. JS相关
```js
$('form).submit(function() {		
  $this = $(this);
    var ERR = 0;
    // 表单验证代码
	  ...

  if( ERR == 0 ) {
    $this.attr('onsubmit','return false;');
    $.post($this.attr('pre-action'), $this.serialize(),function(data){
      if(data.status == 1 || data.status == 200) {  // 成功
        var jumpUrl = data.url;
        if(actionjumpUrl){
	      window.location.href=jumpUrl;
	    }else{
          window.location.reload();
        }
      }else { // 失败
        $this.removeAttr('onsubmit');
	  }
    })
  }
  return false;
});
```
**讲解:**
>  1. 通过`$this.attr('pre-action')` 获取提交的url, 这样和默认表单提交很类似.
>  2. 通过 `$this.serialize()` 获取表单中的内容, 是**serialize**函数的用法

##  3. 总结
> 这样即提高了用户体验度, 也实现当JS部分报错造成`form`表单默认提交, 同时也可以在JS中做表单验证和放置重复提交.

Author [@Snaker95][1]

[1]: http://www.sharedsea.com

