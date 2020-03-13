---
title: JS时间函数补充-自定义DateFormat()
categories: [JavaScript]
tags: JavaScript
keywords: [HTML,JS,时间,时间格式化]
description: 在js中, 获取时间的方法已经很多了, 但是作为PHP程序猿早已习惯了PHP中的时间函数`date('Y-m-d H:i:s)`

---

[TOC]

# JS时间函数补充-自定义DateFormat()
在js中, 获取时间的方法已经很多了, 但是作为PHP程序猿早已习惯了PHP中的时间函数`date('Y-m-d H:i:s)`, 在这里, 自己封装一个js中的类是PHP中的`date()`函数
<!--more-->
## 一. 使用方法
> 1. 写入活引入下面的JS代码
> 2. 实例化时间对象, `var date = new Date();`
> 3. 获取时间格式, `var showDate = date.DateFormat(格式化,时间戳)`

## 二. JS代码 实现
```js
// 时间戳转日期格式化
Date.prototype.DateFormat = function() {
    var format = arguments[0];
    var stamptime = arguments[1];
    var today = new Date();
    if( ! format ) {
        format = 'Y-m-d';
    }
    stamptime = stamptime*1000;
    if( ! stamptime ) {
        stamptime = Date.parse(today);
    }
    var myDate = new Date(stamptime);
    if (!isNaN(myDate))
    {
        Year= myDate.getFullYear();//ie火狐下都可以
        Month= myDate.getMonth()+1;
        Month=Month<10?'0'+Month:Month;
        Day = myDate.getDate();
        Day=Day<10?'0'+Day:Day;
        Hour = myDate.getHours();
        Hour=Hour<10?'0'+Hour:Hour;
        Minute = myDate.getMinutes();
        Minute=Minute<10?'0'+Minute:Minute;
        Second = myDate.getSeconds();
        Second=Second<10?'0'+Second:Second;
        var Newdate = null;
        if( format.indexOf('Y') > -1 ) {
            Newdate += Year;
            Newdate += format.substr(format.indexOf('Y')+1,1);
        }
        if( format.indexOf('m') > -1 ) {
            Newdate += Month+format.substr(format.indexOf('m')+1,1);
        }
        if( format.indexOf('d') > -1 ) {
            Newdate += Day+format.substr(format.indexOf('d')+1,1);
        }
        if( format.indexOf('H') > -1 ) {
            Newdate += Hour+format.substr(format.indexOf('H')+1,1);
        }
        if( format.indexOf('i') > -1 ) {
            Newdate += Minute+format.substr(format.indexOf('i')+1,1);
        }
        if( format.indexOf('s') > -1 ) {
            Newdate += Second+format.substr(format.indexOf('s')+1,1);
        }
        return Newdate;
    }
}
// 测试
var oo = new Date();
console.log(oo.DateFormat('Y年m月'));

```
**讲解**
> 1. **参数一** : 时间格式化方式, 支持Y/m/d/H/i/s (年月日时分秒)
> 2. **参数二** : 时间戳 (秒级)
> 3. 默认不传任何参数, 获取本地时间,Y-m-d形式

## 总结
> 功能虽然已经实现, 个人认为还可以优化, 希望技术牛们帮助改进...


> &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;~^_^*小生有礼了*~

Author [@Snaker95][1]

[1]: http://www.sharedsea.com


