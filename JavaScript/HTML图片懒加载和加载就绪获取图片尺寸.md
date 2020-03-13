---
title: HTML图片懒加载和加载就绪获取图片尺寸
categories: [JavaScript]
tags: JavaScript
keywords: [HTML,JS,图片懒加载,等比例缩放]
description: 现在的网站越来越花哨, 图片加载越来越多, 这样加载速度就会越来越慢了, 因此, 图片懒加载就很流行咯~

---

[TOC]

# HTML图片懒加载和加载就绪获取图片尺寸
现在的网站越来越花哨, 图片加载越来越多, 这样加载速度就会越来越慢了, 因此, 图片懒加载就很流行咯~
<!--more-->
## 一. 基本思路
> **1.** 把`img`标签中的`src`属性**删掉**, 此时, 添加属性 data_src, 把图片链接放在该属性下
> 
> **2.** 通过使用`img`标签添加背景图片, 充当懒加载前的显示图片
>
> **3.** 当该`img`标签在浏览器**显示区内**时, 通过`js`实现把`img`标签 中的`data_src`的链接赋值到`img`标签中的`src`属性
> **注:** 浏览器显示区如何判断

## 二. JS代码 - 页面懒加载
```js
// 页面懒加载 (改进版见*五*)
function loadlazy(){
  var itv = setTimeout(function(){
    var winHeight = $(window).height();
    var scrolltop = $(window).scrollTop();
    $('.lazyload-img').each(function(){
      // 计算img是否在显示区, 并且 img 没有src属性
      var oTop = $(this).offset().top;
      if( ! $(this).attr('src')
          && (oTop-scrolltop) >= 0
          && (oTop-scrolltop)<winHeight
        ){
        var src = $(this).attr("data_src");
        $(this).attr("src",src);
      }
    })
  },100);
}
    
// 页面滚动加载
$(window).scroll(function(){
    loadlazy();
}
 
// 页面加载完成
$(function() {
    loadlazy();
})
```

## 三. JS代码 - 图片加载就绪获取图片尺寸
```js
/*************************************************************
 * 图片头数据加载就绪获取图片尺寸
 * @version 2011.05.27
 * @author  TangBin
 * @see
 * @param   {String}    图片路径
 * @param   {Function}  尺寸就绪
 * @param   {Function}  加载完毕 (可选)
 * @param   {Function}  加载错误 (可选)
 * @用法:
   Pre_load_img('1.png', function (){
     alert('size ready: width=' + this.width + '; height=' + this.height);
   });
*************************************************************/
var Pre_load_img = (function () {
    var list = [], intervalId = null,

    // 用来执行队列
    tick = function () {
        var i = 0;
        for (; i < list.length; i++) {
            list[i].end ? list.splice(i--, 1) : list[i]();
        };
        !list.length && stop();
    },

    // 停止所有定时器队列
    stop = function () {
        clearInterval(intervalId);
        intervalId = null;
    };

    return function (url, ready, load, error) {
        var onready, width, height, newWidth, newHeight,
            img = new Image();

        img.src = url;

        // 如果图片被缓存，则直接返回缓存数据
        if (img.complete) {
            ready.call(img);
            load && load.call(img);
            return;
        };

        width = img.width;
        height = img.height;

        // 加载错误后的事件
        img.onerror = function () {
            error && error.call(img);
            onready.end = true;
            img = img.onload = img.onerror = null;
        };

        // 图片尺寸就绪
        onready = function () {
            newWidth = img.width;
            newHeight = img.height;
            if( newWidth !== width
                || newHeight !== height
                || newWidth * newHeight > 1024
              ) {
                ready.call(img);
                onready.end = true;
            };
        };
        onready();

        // 完全加载完毕的事件
        img.onload = function () {
            // onload在定时器时间差范围内可能比onready快
            // 这里进行检查并保证onready优先执行
            !onready.end && onready();

            load && load.call(img);

            // IE gif动画会循环执行onload，置空onload即可
            img = img.onload = img.onerror = null;
        };

        // 加入队列中定期执行
        if (!onready.end) {
            list.push(onready);
            // 无论何时只允许出现一个定时器，减少浏览器性能损耗
            if (intervalId === null) intervalId = setInterval(tick, 40);
        };
    };
})();
```
> 此`方法` 抄至*高手大牛之手*
**讲解:** 用法见上

## 四. 图片等比例缩放
```css
.width-100{
  width:auto!important;
  min-height:100%!important;
}
.height-100{
  height:auto!important;
  min-width:100%!important;
}
```

## 五. 总结使用HTML懒加载和等比例缩放
```js
function loadlazy(){
    /*首屏图片直接加载*///页面懒加载
    var itv = setTimeout(function(){
        var winHeight = $(window).height();
        var scrolltop = $(window).scrollTop();
        $('.lazyload-img').each(function(){
            var $this = $(this);
            var src = $this.attr("data_src");
            if( ! $(this).attr('src') && src ) {
                var oTop = $(this).offset().top;
                if( (oTop-scrolltop) >= 0 
                   && (oTop-scrolltop)<winHeight
                  ){
                    // 调用图片预加载func
                    Pre_load_img(src, function (){
                        $this.parent().css('overflow','hidden');
                        // 检测该img是否需要缩放, 在img标签中添加 is-zoom属性
                        var is_zoom = $this.attr('is-zoom);
                        if(is_zoom){
                            var p_height = $this.parent().height();
                            var p_width = $this.parent().width();
                            if( (p_height/p_width) < (this.height/this.width) ) {
                                $this.css('width', p_width);
                                $this.css('height','auto');
                            }else if((p_height/p_width) > (this.height/this.width)) {
                                $this.css('height', p_height);
                                $this.css('width','auto');
                            }
                        }else{
                            if(this.height > this.width) {
                                $this.addClass('height-100');
                            }else if(this.height < this.width){
                                $this.addClass('width-100');
                            }
                        }
                        $this.attr("src",src);
                    });
                }
            }
        })
    },100);
}

// 页面滚动加载
$(window).scroll(function(){
    loadlazy();
}
 
// 页面加载完成
$(function() {
    loadlazy();
})
```

Author [@Snaker95][1]

[1]: http://www.sharedsea.com


