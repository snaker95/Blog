---
title: JS时间函数补充-自定义getDiff()
categories: [JavaScript]
tags: JavaScript
keywords: [HTML,JS,PHP,时间,时间差]
description: 现在web时代中, 使Ajax请求数据来丰富页面内容已经潮的不能潮了, 关于**时间**显示的要求也随着不断精确和人性化, 从最开始只要求显示 `Y-m-d H:i:s` 到 `N分钟前`,隔天之后显示`Y-m-d`

---

[TOC]

#JS时间函数补充-自定义getDiff()
现在web时代中, 使Ajax请求数据来丰富页面内容已经潮的不能潮了, 关于**时间**显示的要求也随着不断精确和人性化, 从最开始只要求显示 `Y-m-d H:i:s` 到 `N分钟前`, 隔天之后显示`Y-m-d`, 此时, 仅仅JS中原有的时间函数并不能满足这一需求, 由于项目需求, 个人写了一段码供大家参考
<!--more-->
## 一. 使用方法
> 1. 写入活引入下面的JS代码
> 2. 实例化时间对象, `var date = new Date();`
> 3. 计算所需日期, `var showDate = date.getDiff(时间戳)`
> 4. 或者在`PHP`中实现

## 二. JS代码 实现
```js
// 格式化时间
Date.prototype.getDiff = function(dtStart) {
    var dtEnd = arguments[1];
    var format = arguments[2];
    var today = new Date();
    var numReg = /^\d+$/;
    if( ! dtEnd || ! numReg.test(dtEnd) ) {
        if( ! format ) {
            format = dtEnd;
        }
        dtEnd = parseInt(today.getTime()/1000);
    }
    if( ! format ) {
        format = 'Y-m-d';
    }

    var sDiff = parseInt(dtEnd - dtStart);
    // 时间戳转日期格式化
    function formatDate(stamptime) {
        var myDate = new Date(stamptime*1000);
        if (!isNaN(myDate))
        {

            var Year= myDate.getFullYear();//ie火狐下都可以
            var Month= myDate.getMonth()+1;
            Month=Month<10?'0'+Month:Month;
            var Day = myDate.getDate();
            Day=Day<10?'0'+Day:Day;
            var Hour = myDate.getHours();
            Hour=Hour<10?'0'+Hour:Hour;
            var Minute = myDate.getMinutes();
            Minute=Minute<10?'0'+Minute:Minute;
            var Second = myDate.getSeconds();
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
    // 计算昨天的时间戳范围
    today.setHours(0);
    today.setMinutes(0);
    today.setSeconds(0);
    today.setMilliseconds(0);
    var oneday = 86400000;
    var yesterdayStart = new Date(today - oneday);
    yesterdayStart = parseInt(yesterdayStart.getTime()/1000);
    yesterdayEnd = yesterdayStart + 86400;

    var strInterval = 'l';
    if(0 <= sDiff &&  sDiff < 60 ) {
        strInterval = 's';
    }else if(60 <= sDiff && sDiff < 3600) {
        strInterval = 'n';
    }else if(3600 <= sDiff && sDiff < 86400) {
        strInterval = 'h';
    }else if(yesterdayStart <= dtStart && dtStart < yesterdayEnd){
        strInterval = 'd';
    }
    switch (strInterval) {
        case 's' :return sDiff+'秒';
        case 'n' :return parseInt(sDiff / 60)+'分钟之前';
        case 'h' :return parseInt(sDiff / 3600)+'小时前';
        case 'd' :return '昨天';
        default : return formatDate(dtStart)
    }
}

```
**讲解**
> 1. 必传参数问题, 默认只传入需要计算的**时间戳**
> 2. 选传参数问题, **`传参二`**可为**结束时间戳**或者当没有**`传参三`**时为**输出格式***('Y-m-d')*;
>  **`传参三`**为**输出格式**;

## 三. PHP代码 实现
```php
//格式化时间格式为：xxx前
class timer{
    private $DateTime;

    public function __construct($time = "now") {
        $this->DateTime = new \DateTime($time);
    }

    public static function easyFormat(\timer $time, $endtime="now") {
        $format = 'Y-m-d';

        if($endtime === 'now' || !($endtime instanceof timer) ) 
            $endtime = new static();
        if($time > $endtime) return $time->DateTime->format($format);
        
        $intval = $endtime->DateTime->diff($time->DateTime);

        // if($intval->y > 0) return $intval->y.'年前';
        // if($intval->m > 0) return $intval->m.'月前';
        if($intval->y > 0 || $intval->m > 0)
            return $time->DateTime->format($format);

        if($intval->d > 0) return $intval->d.'天前';
        if($intval->h > 0) return $intval->h.'小时前';
        if($intval->i > 0) return $intval->i.'分钟前';
        if($intval->s > 0) return $intval->s.'秒前';

        return $time->DateTime->format($format);
    }
}
echo timer::easyFormat(new timer(date('Y-m-d')));
```
**讲解**
> 

## 总结
> 功能虽然已经实现, 个人认为还可以优化, 希望技术牛们帮助改进...


> &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;~^_^*小生有礼了*~

Author [@Snaker95][1]

[1]: http://www.sharedsea.com


