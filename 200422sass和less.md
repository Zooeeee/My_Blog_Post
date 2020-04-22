---
title: 200422sass和less
comments: true
tags:
  - css
categories: css
date: 2020-04-22 12:14:38
---
### Sass和Less都属于css预处理器,css预处理器定义了一种新的语言,其基本思想是,用一种专门的编程语言,为css增加一些编程的特性,如变量,函数,语句,继承等,将css作为目标生成文件.
**sass和scss都是sass的后缀,只是一个是老版本,一个是新版本**
<!--more-->
### Less与Scss的使用与区别
#### 注释 less与scss没区别
单行注释,如// 是不会被编译的
多行注释(css原生注释)/**/,是会被编译的
#### 变量
##### less 使用@
```less
@number:123px;
.box{
  width:@number;
}
// css中结果
.box {
  width: 123px;
}
```
##### scss 使用$
```scss
$number:123px;
.box{
  width: $number;
}
//css中结果
.box {
  width: 123px;
}
```

#### 插值
##### less 使用@{key}
```less
@key:margin;
@i:3;
.box@{i}{
    @{key}:auto;
}
//css
.box3 {
  margin: auto;
}
```
##### scss 使用 #{$key}
```scss
$key : margin;
$i:3;
.box#{$i}{
    #{$key}:auto;
}
//css
.box3 {
  margin: auto;
}
```
#### 作用域
##### less 作用域,就近原则,不在乎声明与使用的先后
```less
@number:123px;
.box4{
    height: @number;
    @number:456px;
    width: @number;
}
//css
.box4 {
  height: 456px;
  width: 456px;
}
```
##### scss 作用域,在乎使用的顺序
```scss
$number:123px;
.box4{
    height: $number;
    $number:456px;
    width: $number;
}
//css
.box4 {
  height: 123px;
  width: 456px;
}
```
#### 选择器嵌套 两者一样
**值得注意的是,如果直接写:hover伪类,那么生成的将是含有空格的代码,意为所有后代,如果只要该类,则要加&**
```less
ul{
    color: #fff;
    li{ color: #fff;
        div{ color: #fff;};
        p{ color: #fff;};
    }
    &:hover{
        color: #909;
    }
}
//css
ul {
  color: #fff;
}
ul li {
  color: #fff;
}
ul li div {
  color: #fff;
}
ul li p {
  color: #fff;
}
ul:hover {
  color: #909;
}
```
##### 属性嵌套(scss才有)
```scss
p{
    font : {
        size:10px;
        family:宋体;
    }
}
//css
p {
  font-size: 10px;
  font-family: 宋体;
}
```

#### 计算
##### less
```less
@num : 10px;
.box6{
    width: @num * 3;
    color:#111 * 2 ;
    // 以前一个单位作为最终单位
    margin: @num + 10rem;
    margin: 10rem + @num ;
    //转义
    padding : 20px / 5;
    padding: ~"20px / 5";
}
//css
.box6 {
  width: 30px;
  color: #222222;
  margin: 20px;
  margin: 20rem;
  padding: 4px;
  padding: 20px / 5;
}
```
##### scss
```scss
$num : 10px;
.box6{
    width: $num * 3;
    color:#111 * 2 ;
    // 不是相同的单位不能编译成功
    margin: $num + 10px;
    // 默认不识别/,如果需要运算,则要用()
    padding : 20px / 5;
    padding: (20px / 5);
}
//css
.box6 {
  width: 30px;
  color: #222222;
  margin: 20px;
  padding: 20px / 5;
  padding: 4px;
}
```

#### 混入
##### less 没加括号的会编译进css文件,加括不编译进css且可以传参
```less
.show{
    display: block;
}
.hide(@color){
    display: none;
    color: @color;
}
.box7{
    width: 100%;
    .show;
    .hide(blue);
}
//css
.show {
  display: block;
}
.box7 {
  width: 100%;
  display: block;
  display: none;
  color: blue;
}
```
##### scss 使用@mixin定义,@include使用
```scss
@mixin show{
    display: block;
}
@mixin hide($color){
    display: none;
    color: $color;
}
.box7{
    width: 100%;
    @include show;
    @include hide(blue);
}
//css
.box7 {
  width: 100%;
  display: block;
  display: none;
  color: blue;
}
```

#### 命名空间
##### less
```less
#namespace {
    .display(){
        display: inline;
    }
}
.box8{
    #namespace.display;
}
//css
.box8 {
  display: inline;
}
```
##### scss
暂无



#### 继承
##### less 不能去掉不需要的类,会在生成的css中还是含有.show
```less
.show{
    display: block;
}
.box5{
    &:extend(.show);
}
.box7{
    &:extend(.show);
}
//css
.show,
.box5,
.box7 {
  display: block;
}
```
##### scss %表示占位符,不在生成代码中编译
```scss
%show{
    display: block;
}
.box5{
    @extend %show;
}
.box7{
    @extend %show;
}
//css
.box5, .box7 {
  display: block;
}
```
#### 合并
##### less
```less
.box6{
    //逗号合并
    background+: url(1.png);
    background+: url(2.png);
    //空格合并
    transform+_: rotate(30deg);
    transform+_: scale(2);
}
//css
.box6 {
  background: url(1.png), url(2.png);
  transform: rotate(30deg) scale(2);
}
```
##### scss 注意,链接和空格链接
```scss
$background : (
    a : url(a.png), 
    b : url(b.png) 
);
$transform : (
   a: rotate(30deg),
   b: scale(2)
);
.box8{
    background: map-values($background);
    transform: zip(map-values($background)...);
}
//css
.box8{
    background: map-values($background);
    transform: zip(map-values($background)...);
}
```



#### media
##### less与scss一样
```less
.box8{
    width:100px;
    @media screen {
        width:600px;
    }
    @media print{
        width:100px;
    }
}
//css
.box8 {
  width: 100px;
}

@media screen {
  .box8 {
    width: 600px;
  }
}

@media print {
  .box8 {
    width: 100px;
  }
}
```


#### 条件
##### less
```less
@count : 5 ;
.get(@cn) when(@cn>4){
    width: @cn;
}
.box11{
    .get(@count);
}
//css
.box11 {
  width: 5;
}
```
##### scss
```scss
$count : 4 ;
.box11{
    @if ($count > 5) {
        width: 100px + $count;
    }
    @else {
        width: 50px + $count;
    }
}
//css
.box11 {
  width: 54px;
}
```


#### 循环
##### less,通过递归,实现循环,比较麻烦,反向输出
```less
@count2 : 0 ;
.get2(@cn) when(@cn < 3 ){
    .get2(@cn+1);
    .box-@{cn}{
        width:100px+@cn;
    }
}
.get2(@count2);
//css  
.box-2 {
  width: 102px;
}
.box-1 {
  width: 101px;
}
.box-0 {
  width: 100px;
}
```
##### scss
```scss
$count2 : 0 ;
@for $i from 0 to 3 {
    .box#{$i}{
        width: 100px + $i;
    }
//css
.box0 {
  width: 100px;
}

.box1 {
  width: 101px;
}

.box2 {
  width: 102px;
}
```

#### 导入,二者相同
##### less
```less
@import 'xxxx.less';
//
@import 'xxxx.scss';
```
