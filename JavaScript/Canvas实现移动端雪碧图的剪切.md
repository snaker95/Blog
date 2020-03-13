---
title: Canvas实现移动端雪碧图的剪切
categories: [JavaScript]
tags: JavaScript
keywords: [HTML,JS,移动端,雪碧图]
description: 在互联网高速发展的现在, 移动端越来越成为主力军, 在开发中, 移动端的图标问题成为影响加载速度的一个原因

---

[TOC]



# Canvas实现移动端雪碧图的剪切

在互联网高速发展的现在, 移动端越来越成为主力军, 在开发中, 移动端的图标问题成为影响加载速度的一个原因, 曾经在PC端的解决方案是雪碧图, 这个大家应该不会陌生, 但是在移动端进行图片icon定位不是很优好, 因为用到的不是px;

这里的解决思路是: 利用雪碧图和canvas把雪碧图中的icon图标使用canvas画出来;

```js
var Icon = function() {
    // 判断是否存在实例
    if (typeof Icon.instance === 'object') {
        return Icon.instance;
    }

    // 其它内容
    this.imgBuffer = {}; // 缓存
    this.textureMap = { // 雪碧图定位数据
        // {x:20, y:20, w:50, h:50},
        // {x:0, y:0, w:80, h:80},
    };
    this.texture=document.getElementById('xuebi-icon'); // 雪碧图element
    this.defaultIcon = '';

    // 缓存 (单例)
    Icon.instance = this;

    this.getCanvas = function(i) {
        if(i in this.imgBuffer) return this.imgBuffer[i];
        if(i in this.textureMap) {
            tTemp=this.textureMap[i];
            if(!('w' in tTemp)) tTemp.w=10;
            if(!('h' in tTemp)) tTemp.h=10;
            if(!('x' in tTemp)) tTemp.x=0;
            if(!('y' in tTemp)) tTemp.y=0;

            tObj=createEmptyCanvas(tTemp.w,tTemp.h);
            tCtx=tObj.ctx;

            tCtx.drawImage(
                this.texture,
                tTemp.x,
                tTemp.y,
                tTemp.w,
                tTemp.h,
                0,
                0,
                tTemp.w,
                tTemp.h
            );
            this.imgBuffer[i]={
                img:tObj.canvas
            };
            return this.imgBuffer[i];
        }
        return {};
    };
    this.getUrl = function(i) {
        var canvas = this.getCanvas(i);
        if('img' in canvas) return canvas['img'].toDataURL();
        return typeof this.defaultIcon === 'object' ? this.defaultIcon.src : this.defaultIcon;
    };

    // 生成空画布
    function createEmptyCanvas(w, h) {
        var
            canvas = document.createElement('canvas'),
            ctx = canvas.getContext('2d');
            canvas.width = w;
            canvas.height = h;
        return {canvas:canvas, ctx:ctx}

    }

}
```



```html
<!DOCTYPE html>
<html>
<head>
    <title>
        雪碧图截图 为 图标ico
    </title>
</head>
<body>
    <canvas id="icon-canvas"></canvas>
    <img id="xuebi-icon" src="././snaker95@100.png" alt="The Tulip" />
    <img id="icon-img" src="">
    <script src="icon.js"> </script>
    <script>
        var img = new Image();
        img.src = document.getElementById('xuebi-icon').src;
        console.log(img.complete)
        img.onload = function() { // 确保雪碧图加载完成
            var IconObj = new Icon(); // 实例化

            // 设置默认图标,
            IconObj.defaultIcon = document.getElementById('xuebi-icon');
            // 设置雪碧图
            IconObj.texture = document.getElementById('xuebi-icon');
            // 设置各图标icon信息 {w:10, h:10, x:0, y:0}
            IconObj.textureMap['csh'] = {w:50, h:50, x:20, y: 20};

            // 通过图标的url, base64
            var icon_1 = IconObj.getUrl('csh');
            var icon_canvas = IconObj.getCanvas('csh');

            IconObj.textureMap['csh'] = {w:2};

            // 测试单例
            var i = new Icon();

            document.getElementById('icon-img').src=icon_1;
        }
    </script>
</body>
</html>

```

