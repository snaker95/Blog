---
title: Javascript中两个变量互换值
categories: [JavaScript]
tags: JavaScript
keywords: [HTML,JS,图片懒加载,等比例缩放]
description: Javascript中两个变量互换值 
---

[TOC]

# Javascript中两个变量互换值
## 方法一:
```js
<script>
var foo = 1; bar =2;
bar = [foo,foo=bar][0];
console.log(foo);
console.log(bar);
</script>
```

## 方法二:
```js
# 存在兼容性问题
[a, b] = [b, a];
```

## 方法三:
```js
c = a;
a = b;
b = c;
```

Author [@Snaker95][1]

[1]: http://www.sharedsea.com




