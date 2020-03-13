---
title: Ajax请求列表时的效果
categories: [JavaScript]
tags: JavaScript
keywords: [HTML,JS,解析URL]
description: 在数据列表中, ajax请求为常用的一种方法; 由于一个项目中,列表部分占比较大的比例, 所以,重新一个ajax请求列表时很重要的, 这样既能减少代码量, 又能在后期维护中提高效率;
---

[TOC]

# Ajax请求列表时的效果
在数据列表中, ajax请求为常用的一种方法; 由于一个项目中,列表部分占比较大的比例, 所以,重新一个ajax请求列表时很重要的, 这样既能减少代码量, 又能在后期维护中提高效率; 
## js代码:
```js
$.ajax_list = (function($acfg,$scfg) {
  /* --- AJAX 相关参数 --- */
  var acfg = {
    'type'      :   'post'
    ,'url'       :   ''
    ,'data'      :   {}
    ,'dataType'  :   'json'
    ,'async'     :   true
  }
  /* --- 效果 相关参数 --- */
  var scfg = {
    's_type'    :     'insert' // 请求回的数据是追加(insert),或者替换(replace)
    ,'s_dom'     :     'body'  // 操作的dom
    ,'s_break'   :     true   // 是否终止之前未完成的ajax请求
    ,'s_warn'   :      ''      // 加载过程中的友好提示(class样式)
  }
  var _callback = function(){}
  /* ---- 友好提示的样式 ---- */
  var warn_icon = "data:image/svg+xml;charset=utf-8,%3Csvg%20viewBox%3D'0%200%20120%20120'%20xmlns%3D'http%3A%2F%2Fwww.w3.org%2F2000%2Fsvg'%20xmlns%3Axlink%3D'http%3A%2F%2Fwww.w3.org%2F1999%2Fxlink'%3E%3Cdefs%3E%3Cline%20id%3D'l'%20x1%3D'60'%20x2%3D'60'%20y1%3D'7'%20y2%3D'27'%20stroke%3D'%236c6c6c'%20stroke-width%3D'11'%20stroke-linecap%3D'round'%2F%3E%3C%2Fdefs%3E%3Cg%3E%3Cuse%20xlink%3Ahref%3D'%23l'%20opacity%3D'.27'%2F%3E%3Cuse%20xlink%3Ahref%3D'%23l'%20opacity%3D'.27'%20transform%3D'rotate(30%2060%2C60)'%2F%3E%3Cuse%20xlink%3Ahref%3D'%23l'%20opacity%3D'.27'%20transform%3D'rotate(60%2060%2C60)'%2F%3E%3Cuse%20xlink%3Ahref%3D'%23l'%20opacity%3D'.27'%20transform%3D'rotate(90%2060%2C60)'%2F%3E%3Cuse%20xlink%3Ahref%3D'%23l'%20opacity%3D'.27'%20transform%3D'rotate(120%2060%2C60)'%2F%3E%3Cuse%20xlink%3Ahref%3D'%23l'%20opacity%3D'.27'%20transform%3D'rotate(150%2060%2C60)'%2F%3E%3Cuse%20xlink%3Ahref%3D'%23l'%20opacity%3D'.37'%20transform%3D'rotate(180%2060%2C60)'%2F%3E%3Cuse%20xlink%3Ahref%3D'%23l'%20opacity%3D'.46'%20transform%3D'rotate(210%2060%2C60)'%2F%3E%3Cuse%20xlink%3Ahref%3D'%23l'%20opacity%3D'.56'%20transform%3D'rotate(240%2060%2C60)'%2F%3E%3Cuse%20xlink%3Ahref%3D'%23l'%20opacity%3D'.66'%20transform%3D'rotate(270%2060%2C60)'%2F%3E%3Cuse%20xlink%3Ahref%3D'%23l'%20opacity%3D'.75'%20transform%3D'rotate(300%2060%2C60)'%2F%3E%3Cuse%20xlink%3Ahref%3D'%23l'%20opacity%3D'.85'%20transform%3D'rotate(330%2060%2C60)'%2F%3E%3C%2Fg%3E%3C%2Fsvg%3E";
  var warn_css = '<style>.preloader{display:inline-block;width:1.5rem;height:1.5rem;-webkit-transform-origin:50%;transform-origin:50%;-webkit-animation:preloader-spin 1s steps(12,end) infinite;animation:preloader-spin 1s steps(12,end) infinite}.preloader:after{display:block;content:"";width:100%;height:100%;background-image:url("'+warn_icon+'");background-position:50%;background-size:100%;background-repeat:no-repeat}</style>';
    $('head').append(warn_css);

    return function( $acfg , $scfg, $func) {
    acfg = merge(acfg,$acfg);
        scfg = merge(scfg,$scfg);
        $('.scroll-preloader').remove();
        if(scfg.s_break && typeof(xhr) !== 'undefined' && xhr !== null && typeof(xhr) === 'object') xhr.abort(); // 中断上一次请求
        xhr = $.ajax({
            type            :   acfg['type']
            ,url            :   acfg['url']
            ,data           :   acfg['data']
            ,dataType       :   acfg['dataType']
            ,async          :   acfg['async']
            ,beforeSend     :   function(e) {
              /* ---- 替换时提前清空dom ---- */
              if(scfg['s_type'] === 'replace'){
                $(scfg['s_dom']).html('');
              }
    
              /* ---- 友好提示 ---- */
              var warn_str = '<div class="scroll-preloader" style="margin:.5rem;text-align:center;"><div class="preloader"></div></div>';
              $(scfg['s_dom']).append(warn_str);
    
            }
            ,success        :   function(e) {
                // func(e); return;
            }
            ,error        :   function(e) {
                // func(e); return;
            }
            ,complete       :   function(e) {
              xhr = null;
              $('.scroll-preloader').remove();
              $func(e);
            }
        });
    }

    function merge() {
        var merge;
        var numargs = arguments.length;
        if(numargs > 1){
          try{
            merge = Object.assign(arguments[0],arguments[1]);
          }catch(e){
            merge = $.extend(arguments[0],arguments[1]);
          }
        }else{
          merge = arguments[0];
        }
        return merge;
    }
})();
```
* 详细可见: [Snaker95`GitHub](https://github.com/snaker95/JS/blob/master/scrollpage/js/nextajax.js)

Author [@Snaker95][1]

[1]: http://www.sharedsea.com





