---
title: 200422PostCSS
comments: true
tags:
  - css
categories: css
date: 2020-04-22 19:04:43
---
**PostCSS本身是一个功能比较单一的工具.它提供了一种方式用js代码来处理css.利用postCSS可以实现一些工程化的操作,如:自动添加浏览器前缀,代码合并,代码压缩等**
#### 环境安装
npm install  postcss-cli  -g

#### 常用命令-o -w
```
postcss src/demo.css -o dist/demo.css -w
```
* -o指定输出文件
* -w 使用观察者模式,热编译 
* 在src目录下创建 postcss.config.js配置文件,类似webpack

#### autoprefixer
**为css属性添加浏览器前缀**
```js
// 配置文件
const autoprefixer = require('autoprefixer');
module.exports = {
    plugins : [
        autoprefixer({
            browsers : ['> 0%']
        })
    ]
}
```
```css
div{
    width:5px;
    height: 200px;
    transform: rotate(3deg);
}
//编译后
div{
    width:5px;
    height: 200px;
    -webkit-transform: rotate(3deg);
       -moz-transform: rotate(3deg);
        -ms-transform: rotate(3deg);
         -o-transform: rotate(3deg);
            transform: rotate(3deg);
}
```
#### postcss-import
**将源码中的@import的css文件合并成一个css文件**
```js
const pcImport = require('postcss-import');
module.exports = {
    plugins : [
        pcImport
    ]
}
```

#### cssnano
**丑化css代码**
```js
const pcImport = require('cssnano');
module.exports = {
    plugins : [
       cssnano
    ]
}
```

#### css-next
**将最新的css语法翻译成支持老版本的语法**
```js
const cssnext = require('postcss-cssnext');
module.exports = {
    plugins : [
        cssnext
    ]
}
```
```css
:root{
    --color:red;
}
div{
    background-color: var(--color);
}
//output
div{
    background-color: red;
}
```


#### stylelint 
**css语法检测**
```js
const stylelint = require('stylelint');
module.exports = {
    plugins : [
        stylelint({
            "rules":{
                "color-no-invalid-hex":true
            }
        })
    ]
}
```
```css
div{
    color:#ff;
}
// 2:11    ‼  Unexpected invalid hex color "#ff" (color-no-invalid-hex) [stylelint]
```

#### postcss-sprites
**自动生成精灵图/雪碧图**
```js
const sprites = require('postcss-sprites');
module.exports = {
    plugins : [
        sprites({
            spritePath : './dist'  //精灵图输出地址
        })
    ]
}
```
```css
div{
    background-image: url(./img/1.jpg);
}

span{
    background-image: url(./img/2.jpg);
}
//output
div{
    background-image: url(../dist/sprite.png);
    background-position: 0px 0px;
    background-size: 1920px 960px;
}

span{
    background-image: url(../dist/sprite.png);
    background-position: -960px 0px;
    background-size: 1920px 960px;
}
```