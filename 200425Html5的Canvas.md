---
title: 200425Html5的Canvas
comments: true
tags:
  - html
categories: Html
date: 2020-04-25 21:58:02
---
> H5的新特性,在低版本浏览器中不支持
## 创建一个画布
```html
<canvas id="myCanvas" width="200" height="100"></canvas>
```
## 通过 JavaScript 来绘制
> canvas 元素本身是没有绘图能力的。所有的绘制工作必须在 JavaScript 内部完成
**getContext("2d") 对象是内建的 HTML5 对象，拥有多种绘制路径、矩形、圆形、字符以及添加图像的方法。**
```html
<body>
    <canvas id="myCanvas" width="200px" height="200px"></canvas>
    <script >
        var canvas = document.getElementById("myCanvas");
        var content=canvas.getContext("2d"); 
        content.fillStyle="#FF0000";
        content.fillRect(0,0,150,75); 
    </script>
</body>
```
## 放置图像
```html
<script type="text/javascript">

var c=document.getElementById("myCanvas");
var cxt=c.getContext("2d");
var img=new Image()
img.src="flower.png"
cxt.drawImage(img,0,0);

</script>
```
