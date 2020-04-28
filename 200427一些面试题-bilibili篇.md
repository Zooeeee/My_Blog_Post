---
title: 200427一些面试题(bilibili篇)
comments: true
tags:
  - 面试
  - 前端
categories: Js
date: 2020-04-27 11:05:45
---
**根据网上一篇大厂的面试题总结[地址](https://segmentfault.com/a/1190000020296556)**
## 详述es5 es6中的作用域和闭包（es5全局+函数级，函数化闭包，es6块级）
### ES5
#### ES5的作用域
> 变量起作用的范围，js中能创建作用域的只能是函数
```js
{
  let a = 1;
  var b = 2;
}
console.log(a); // a is not defined
console.log(b); // 2
```
var的作用域就是所在的函数体
在某个函数中使用var声明变量，那个变量就将被视作一个局部变量，只存在于函数中.
let的作用域就是所在的代码块
#### 作用域链
```js
console.log(a); // a is not defined
function test() {
   a = 1;
}
test();
```
1. js中存在全局执行环境和由函数形成的局部执行环境这两种，统称为执行环境（这里可以理解成作用域）；
2. 执行环境都会对应一个变量对象，包含当前环境的变量和函数（函数中的参数也作为函数执行环境的变量，即函数所在作用域的局部变量，函数的活动对象包括this、arguments以及内部的变量和函数）；
3. 只有当*函数执行时*会形成作用域链，作用域链的前端始终是当前执行代码所在的执行环境对应的变量对象，往后是下一个（外部）变量对象，直到最外边的全局执行环境的变量对象（所谓的作用域链就是变量对象组成的一条线）；
4. 变量对象中变量的解析查找就是沿着作用域链一级一级查找；
5. 如果执行环境中的变量没有用var声明，那么在*函数执行时（这样才会形成作用域链）会沿着作用域链一级一级查找变量对象*，如果没有找到则会在全局变量对象中声明该变量并初始化。
以上代码可以改成
```js
function test() {
   a = 1;
}
test();
console.log(a); // 1
```
#### 闭包
```js
   var arr = [];
    for(var i = 0; i<10; i++) {
        arr.push(function(){
        console.log(i);
    })
}
    arr[0]();  //10  而不是0
    arr[1]();  //10  而不是1
```
> 分析： 数组中每个函数如果执行时其作用域链都会保存全局执行环境对应的变量对象，所以函数执行时函数内部的i变量会沿着作用域链找到全局执行环境中的变量，此时全局执行环境中的变量i为10，所以都会输出10.
> 如果想要输出0,1 应使用let 变量i是let声明的，当前的i只在本轮循环有效，所以每一次循环的i其实都是一个新的变量
如果规定要用var来实现预期效果的话
1. 再定义一个循环,来覆盖i值
```js
var arr = [];
for(var i = 0; i<10; i++) {
    arr.push(function(){
    console.log(i);
})
}

for(var i = 0; i<2; i++) {
    arr[i]();
}
// 0
// 1
```
2. 执行匿名函数,直接在当时输出
```js
var arr = [];
for(var i = 0; i<2; i++) {
    arr.push(
    function(){
    console.log(i);
    }()
)
}
//0
//1
```

#### 变量提升
分为预解析阶段和执行阶段
在预解析阶段，会将所有的*变量声明*（只提升声明不提升赋值）以及*函数声明*（指整个函数），提升到其所在的作用域的最顶上，一般会先提升函数声明再提升变量声明。
> 注意区分函数声明和函数表达式声明的区别
> 在变量提升条件下函数表达式和一般变量的声明的规则是一样的。下面的条件式声明章节还会用案例作对比
**当创建一个执行上下文时,会先检查当前上下文的函数声明，也就是使用function关键字声明的函数。在变量对象中以函数名建立一个属性，属性值为指向该函数所在内存地址的引用.然后检查当前上下文中的变量声明，每找到一个变量声明，就在变量对象中以变量名建立一个属性，属性值为undefined**
```js
x = 5; // 变量 x 设置为 5 
console.log(5);
var x; // 声明 x,值得注意的是,let不能,const不能修改所以也不能
```
对于函数来说,只有声明式函数可以变量提升,而函数表达式则不行
```js

// 函数表达式
fn();
// 报错
// //函数声明会被提升（是指整个函数都会被提升）
// var fn = function() {
//   console.log('nihao');
// }
//可以运行
//函数表达式不会被提升（是指只会提升声明该函数的变量）
function fn() {
    console.log('nihao');
  }
```
##### 变量和函数同名
函数覆盖变量
```js
console.log(typeof f);  // function
function f(){};
console.log(typeof f); // function
var f;
console.log(typeof f); // function
// 另一段js
onsole.log(typeof a); // function
function a() { }
console.log(typeof a); // function
var a = '';
console.log(typeof a);  // string
```
> 只提升函数对应变量，其他变量直接不提升，同时将变量的声明var去掉（在一般定义过程中不推荐使用同名变量）
##### 函数和函数同名
都提升，但是后面的会覆盖前面的
```js
func(); // second func
function func(){
    console.log("first func");
}
function func(){
    console.log("second func");
}
```
##### 变量和变量同名
// 一般的变量同名对于变量的提升没有影响，因为提升的只是变量的声明，不会提升变量的赋值
```js
console.log(typeof a); // undefined
var a = 'abc';
console.log(a); // 'abc'
var a = 1;
console.log(a); // 1
```
>JavaScript从来不会告诉你是否多次声明了同一个变量；遇到这种情况，它只会对后续的声明视而不见（不过，它会执行后续声明中的变量初始化）。
#### ES5块级作用域中的函数声明
> ES5 规定，函数只能在顶层作用域和函数作用域之中声明，不能在块级作用域声明。如果确实需要，也应该写成函数表达式，而不是函数声明语句。
> 条件式变量声明可以被提升。
```js
console.log( num ); // undefined
if ( false ) {
   var num = 123;
}
console.log( num ); // undefined
```
### ES6的作用域
#### 块级作用域
> 块级作用域就是包含在{}里面的
#### let和const都存在暂时性死区
只要块级作用域内存在let命令，它所声明的变量就“绑定”（binding）这个区域，不再受外部的影响。
“暂时性死区”是指在使用let命令声明变量之前，该变量都是不可用的
##### let 和 const相同点
1. 支持块级作用域；
2. 变量不能提升；
3. 存在暂时性死区
4. 不可重复声明

#### ES6中顶层对象
* 现状： ES5顶层对象很混乱
浏览器里面，顶层对象是window，但 Node 和 Web Worker 没有window。
浏览器和 Web Worker 里面，self也指向顶层对象，但是Node没有self。
Node 里面，顶层对象是global，但其他环境都不支持。
* ES6的变动
ES6 为了改变这一点，一方面规定，为了保持兼容性，var命令和function命令声明的全局变量，依旧是顶层对象的属性；另一方面规定，let命令、const命令、class命令声明的全局变量，不属于顶层对象的属性。也就是说，从 ES6 开始，全局变量将逐步与顶层对象的属性脱钩。
```js
//es6
let test = 'out';
function  f(){
  test = 'in';
  console.log(global.test);// undefined
}
f();
console.log(test);// in
// es5
test = 'out';
function  f(){
  test = 'in';
  console.log(global.test); // in
}
f();
console.log(test);// in
```
上下两个demo的区别就是test有没有用let声明，当使用let声明时，浏览器会认为当前的环境是ES6环境，所以声明的变量即使赋值到不到global；相反如果没用let声明test，默认当前环境是ES5。
```js
   var num = 123;
   function f1() {
       console.log( num );
    }

   function f2() {
        num = 456;
        f1();
    }

f2();//456
```
```js
// 此处我能给出的解释是,因为变量提升,
// 第一个函数内的test被f()内给定义了且变量提升了,但在那行代码还未赋值,所以undefined
//第二个函数,由于函数内部找不到,于是顺着作用域链找到了外面的test2值
var test = 'out';
function  f(){
  console.log(test);// undefined
  var test = 'in';
}
f();

var test2 = 'out'
function  g(){
    console.log(test2);// out
}
g();
```
## 详述输入url到页面渲染完成
1. DNS解析URL的过程
   1. 输入url地址
   2. 浏览器根据域名查找IP地址
   3. 浏览器发送HTTP请求
2. 浏览器与服务器交互过程
   1. 首先浏览器利用tcp协议通过三次握手与服务器建立连接
   2. 浏览器根据解析到的IP地址和端口号发起http的get请求.
   3. 服务器接收到http请求之后，开始搜索html页面，并使用http返回响应报文
   4. 若状态码为200显示响应成功，浏览器接收到返回的html页面之后，开始进行页面的渲染
3. 浏览器页面渲染过程
   1. 浏览器根据深度遍历的方式把html节点遍历成dom 树
   2. 将css解析成CSS DOM树
   3. 将dom树和CSS DOM树构造成render树
   4. JS根据得到的render树 计算所有节点在屏幕中的位置，进行布局（回流）
   >创建渲染树后，下一步就是布局（Layout），或者叫回流（reflow，relayout），这个过程就是通过渲染树中的渲染对象的信息，计算出每一个渲染对象的位置和尺寸，将其安置在浏览器窗口的正确位置，而有些时候我们会在文档布局完成后对DOM进行修改，这时候可能需要重新进行布局，也可称为回流，本质上还是一个布局的过程，每一个渲染对象都有一个布局或回流的方法，实现其布局或回流。
   5. 遍历render树并调用硬件API绘制所有节点（重绘）
      1. 从DOM树的根节点开始遍历每个可见的节点。
      2. 对于每个可见的节点，找到CSS树中的对应的规则，并且应用他们。
      3. 根据每个可见的节点及其对应的样式，组合生成渲染树。

## JS引擎的执行机制：探究EventLoop（含Macro Task和Micro Task）
> Event Loop：事件循环
> Micro Task：微任务
> Macro Task：宏任务
### 单线程
>javascript是一门单线程语言，在最新的HTML5中提出了Web-Worker，但javascript是单线程这一核心仍未改变。与它的用途有关。作为浏览器脚本语言，JavaScript的主要用途是与用户互动，以及操作DOM。这决定了它只能是单线程，否则会带来很复杂的同步问题。比如，假定JavaScript同时有两个线程，一个线程在某个DOM节点上添加内容，另一个线程删除了这个节点，这时浏览器应该以哪个线程为准？
**所以，为了避免复杂性，从一诞生，JavaScript就是单线程，这已经成了这门语言的核心特征，将来也不会改变。**
单线程就意味着，所有任务需要排队。
1. 同步任务指的是，在主线程上排队执行的任务，只有前一个任务执行完毕，才能执行后一个任务；
2. 异步任务指的是，不进入主线程、而进入"任务队列"（task queue）的任务，只有"任务队列"通知主线程，某个异步任务可以执行了，该任务才会进入主线程执行。

### 异步机制
1. 所有同步任务都在主线程上执行，形成一个执行栈（execution context stack）。
2. 主线程之外，还存在一个"任务队列"（task queue）。只要异步任务有了运行结果，就在"任务队列"之中放置一个事件。
3. 一旦"执行栈"中的所有同步任务执行完毕，系统就会读取"任务队列"，看看里面有哪些事件。那些对应的异步任务，于是结束等待状态，进入执行栈，开始执行。
4. 主线程不断重复上面的第三步。

### JS引擎执行模型
> 从宏观角度讲, js 的执行是单线程的. 所有的异步结果都是通过 “任务队列(Task Queue)” 来调度被调度. 消息队列中存放的是一个个的任务(Task). 规范中规定, Task 分为两大类, 分别是 Macro Task（宏任务） 和 Micro Task（微任务）, 并且每个 Macro Task 结束后, 都要清空所有的 Micro Task.
* Macro Task  宏任务
  * setTimeout
  * setInterval
  * setImmediate
  * I/O
  * UI rendering
* Micro Task  微任务
  * process.nextTick
  * Promise
  * Object.observe
  * MutaionObserver
所说的, ‘每个 MacroTask 结束后, 都要清空所有的 Micro Task‘. 引擎会遍历 Macro Task Queue, 对于每个 MacroTask 执行完毕后都要遍历执行 Tick Task Queue 的所有任务, 紧接着再遍历 MicroTask Queue 的所有任务. (nextTick 会优于 Promise执行)
**1个主线程+n个任务队列，浏览器异步处理后推入队列，循环处理，一个macroTask后跟全部microtask**
```js
setTimeout(() => {
  console.log("setTimeOut是宏任务,排序更低");
}, 0);
new Promise((resolve,reject)=>{
   console.log("promise中的是同步运行,第一个出来")
   resolve("then里面的是微任务,后运行");
  // reject(2);
}).then((value)=>{
  console.log(value);
},(error)=>{
  console.log(error);
})
console.log("外面的是同步运行,第二个出来")
/*
promise中的是同步运行,第一个出来
外面的是同步运行,第二个出来
then里面的是微任务,后运行
setTimeOut是宏任务,排序更低
*/
```


## Promise
* promise是一个对象，对象和函数的区别就是对象可以保存状态，函数不可以（闭包除外)
* 并未剥夺函数return的能力，因此无需层层传递callback，进行回调获取数据
* 代码风格，容易理解，便于维护
* 多个异步等待合并便于解决
```js
new Promise(
  function (resolve, reject) {
    // 一段耗时的异步操作
    resolve('成功') // 数据处理完成
    // reject('失败') // 数据处理出错
  }
).then(
  (res) => {console.log(res)},  // 成功
  (err) => {console.log(err)} // 失败
)
```
* resolve作用是，将Promise对象的状态从“未完成”变为“成功”（即从 pending 变为 resolved），在异步操作成功时调用，并将异步操作的结果，作为参数传递出去；
* reject作用是，将Promise对象的状态从“未完成”变为“失败”（即从 pending 变为 rejected），在异步操作失败时调用，并将异步操作报出的错误，作为参数传递出去。
* 三个状态
  1. pending[待定]初始状态
  2. fulfilled[实现]操作成功
  3. rejected[被否决]操作失败
  当promise状态发生改变，就会触发then()里的响应函数处理后续步骤；
  promise状态一经改变，不会再变。
* promise的对象状态一旦改变,只有两种可能
  * 从pending变为fulfilled
  * 从pending变为rejected。
  * 这两种情况只要发生，状态就凝固了，不会再变了。
```js
new Promise(resolve => {
  setTimeout(() => {
    resolve('hello')
  }, 2000)
}).then(val => {
  console.log(val) //  参数val = 'hello'
  return new Promise(resolve => {
    setTimeout(() => {
      resolve('world')
    }, 2000)
  })
}).then(val => {
  console.log(val) // 参数val = 'world'
})
```
> 作为使用,最好返回一个新的promise对象,这样可以做到链式调用

## Promise.all
> Promise.all可以将多个Promise实例包装成一个新的Promise实例。同时，成功和失败的返回值是不同的，成功的时候返回的是一个结果数组，而失败的时候则返回最先被reject失败状态的值。
```js
let p1 = new Promise((resolve, reject) => {
  resolve('成功了')
})

let p2 = new Promise((resolve, reject) => {
  resolve('success')
})

let p3 = new Promise((resolve, reject) => {
  reject('fail')
})

Promise.all([p1, p2]).then((result) => {
  console.log(result)               //['成功了', 'success']
}).catch((error) => {
  console.log(error)
})

Promise.all([p1,p3,p2]).then((result) => {
  console.log(result)
}).catch((error) => {
  console.log(error)      // 失败了，打出 '失败'
})
/*
[ '成功了', 'success' ]
fail
*/
```

## Promise.race的使用
> 顾名思义，Promse.race就是赛跑的意思，意思就是说，Promise.race([p1, p2, p3])里面哪个结果获得的快，就返回那个结果，不管结果本身是成功状态还是失败状态。

## 如何让Promise.all在抛出异常后依然有效
> 在上文中，我们知道Promise.all在处理多个Promise实例时，如果一个失败，就只能拿到第一个失败的结果，其余成功的结果都无法拿到，那有什么办法能在reject后依旧拿到所有执行结果呢？有，利用catch方法。
> 首先我们需要知道Promise的状态具有可传递性，其次**catch方法在执行后也会返回一个状态为resolved的新Promise实例**，所以我们只要将可能reject的Promise实例先catch一遍就可以了，就像做一次状态预加工：
```js
let p1 = new Promise((resolve, reject) => {
  reject('fail')
})

let p2 = new Promise((resolve, reject) => {
  resolve('成功了')
})

let promiseArr = [p1,p2];
// 预处理,捕获error并作为resolved返回
let = handledArr = promiseArr.map((item)=>{
  return item.catch(error => {
    return error;
  })
})
// 真正处理
Promise.all(handledArr).then((result) => {
  console.log(result)               //['成功了', 'success']
}).catch((error) => {
  console.log(error)
})
```

## 什么是VueX 
> Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。
* State: 单一状态树,存储状态数据
* Getter: 类似于计算属性
* Mutation: 改变状态的唯一方法,多用于同步处理,使用commit调用
* Action: Action 提交的是 mutation，而不是直接变更状态。所以action常用于异步操作,使用dispatch调用
* Module: 由于使用单一状态树，应用的所有状态会集中到一个比较大的对象。当应用变得非常复杂时，store 对象就有可能变得相当臃肿。为了解决以上问题，Vuex 允许我们将 store 分割成模块（module）。每个模块拥有自己的 state、mutation、action、getter、甚至是嵌套子模块——从上至下进行同样方式的分割：

## 详述Vue的双向数据绑定原理 
主要通过对于对象属性的"劫持"来实现,利用Object.defineProperty方法重写某个属性的set方法,然后绑定监听事件,一旦值发生改变,就触发这个某个属性的set方法,实现双向绑定.
```html
<body>
    <input type="text" id="inp" />
    <div id="box"></div>
<script>
    let dInp = document.getElementById('inp');
    let dBox = document.getElementById('box');
    let obj = {};

    Object.defineProperty(obj,'name',{
        configurable: true,
        enumerable: true,
        get: ()=>{
            return val;
        },
        set: (newVal)=>{
            dInp.value = newVal;
            dBox.innerHTML = newVal;
        }
    });

    dInp.addEventListener('input', function(e){
       obj.name = e.target.value
    })
</script>
</body>
```

## Vue的优势
Vue.js是一个**构建数据驱动**的 web 界面的**渐进式框架**。Vue.js 的目标是通过尽可能简单的 API 实现响应的数据绑定和组合的视图组件。核心是一个**响应的数据绑定系统**。
* 轻量级的框架
* 双向数据绑定
* 指令
* 插件化

## SEO
意义:
> 　为了提升网页在搜索引擎自然搜索结果中的收录数量以及排序位置而做的优化行为。简言之，就是希望百度等搜索引擎能多多我们收录精心制作后的网站，并且在别人访问时网站能排在前面。
整体SEO方法:
1. 对网站的标题、关键字、描述精心设置，反映网站的定位，让搜索引擎明白网站是做什么的；
2. 网站内容优化：内容与关键字的对应，增加关键字的密度；
3. 在网站上合理设置Robot.txt文件；
4. 生成针对搜索引擎友好的网站地图；
5. 增加外部链接，到各个网站上宣传；

## 前端SEO
1. 网站结构布局优化：尽量简单、开门见山，提倡扁平化结构。
2. 网站内容优化：内容与关键字的对应，增加关键字的密度；
3. 生成针对搜索引擎友好的网站地图；
4. 增加外部链接，到各个网站上宣传；
5. 代码优化

### 代码优化
1. html
   1. 标签的有开有合。
   2. 避免冗余代码，例如去除空格字符。
   3. 合理利用标签语义化。
   4. 合理的嵌套规则，避免行元素内嵌套块元素。
   5. img标签内需要添加title属性和alt属性。
   6. a标签内需要添加title属性。
   7. Meta标签的优化（过去搜索引擎优化的重要手法，现在已经不是关键因素，但仍不可忽略）主要包括： Meta description、Meta keywords的设置　　关键字密度要适度，通常为2%-8%，也就是说你的关键字必须 在 页面中出现若干次，或者在搜索引擎允许的范围内，要避免堆砌关键字。
   8. <title>页面标题，必须列出信息的标题、网站的名称以及相关关键字，避免堆关键字。
   9. 合理使用注释
   10. 尽量少使用iframe框架,因为“蜘蛛”一般不会读取其中的内容
   11. 文本缩进不要使用特殊符号 &nbsp; 应当使用CSS进行设置。版权符号不要使用特殊符号 &copy; 可以直接使用输入法，拼“banquan”，选择序号5就能打出版权符号©。
2.  css
    1.  避免将css代码写在标签内。
    2.  如果css代码量少，可直接写在头部。否则请使用外部引入的方式。
    3.  请不要使用通配符选择器 *{margin:0;padding:0;} 这不仅仅因为它是缓慢和低效率的方法，而且还会导致一 些不必要的元素也重置了外边距和内边距。
    4.  css代码缩写可以提高你写代码的速度，精简你的代码量，包括margin，padding，border ，font， background和颜色值等。
    5.  利用css继承，如果一个父元素内有多个子元素拥有相同的样式，可将相同的样式定义在元素上。
    6.  如果多个元素拥有相同的样式，可定义一个通用的class或使用群组选择器。
    7.  使用背景图合并技术。
    8.  适当的代码注释。
    9.  谨慎使用 display：none ：对于不想显示的文字内容，应当设置z-index或设置到浏览器显示器之外。因为搜索引擎会过滤掉display:none其中的内容。
3.  js
    1.  采用外部引入的方法。
    2.  合理合并JS代码，可减少服务器的压力。
    3.  良好的JS代码习惯。例如：减少页面重绘，减少作用域链上的查找次数。

## 详解回流和重绘及优化
### 回流
我们通过构造渲染树，我们将可见DOM节点以及它对应的样式结合起来，可是我们还需要计算它们在设备视口(viewport)内的确切位置和大小，这个计算的阶段就是回流。
为了弄清每个对象在网站上的确切大小和位置，浏览器从渲染树的根节点开始遍历，我们可以以下面这个实例来表示：
```html
<!DOCTYPE html>
<html>
  <head>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <title>Critial Path: Hello world!</title>
  </head>
  <body>
    <div style="width: 50%">
      <div style="width: 50%">Hello world!</div>
    </div>
  </body>
</html>
```
我们可以看到，第一个div将节点的显示尺寸设置为视口宽度的50%，第二个div将其尺寸设置为父节点的50%。而在回流这个阶段，我们就需要根据视口具体的宽度，将其转为实际的像素值
### 重绘
最终，我们通过构造渲染树和回流阶段，我们知道了哪些节点是可见的，以及可见节点的样式和具体的几何信息(位置、大小)，那么我们就可以将渲染树的每个节点都转换为屏幕上的实际像素，这个阶段就叫做重绘节点。
###  何时发生回流重绘
既然知道了浏览器的渲染过程后，我们就来探讨下，何时会发生回流重绘。
我们前面知道了，回流这一阶段主要是计算节点的位置和几何信息，那么当页面布局和几何信息发生变化的时候，就需要回流。比如以下情况：
* 添加或删除可见的DOM元素
* 元素的位置发生变化
* 元素的尺寸发生变化（包括外边距、内边框、边框大小、高度和宽度等）
* 内容发生变化，比如文本变化或图片被另一个不同尺寸的图片所替代。
* 页面一开始渲染的时候（这肯定避免不了）
* 浏览器的窗口尺寸变化（因为回流是根据视口的大小来计算元素的位置和大小的）
注意：回流一定会触发重绘，而重绘不一定会回流.
根据改变的范围和程度，渲染树中或大或小的部分需要重新计算，有些改变会触发整个页面的重排，比如，滚动条出现的时候或者修改了根节点。回流是对物理尺寸的变更，回流一定会重绘，重绘不一定回流，因此尽量减少回流次数，将元素移出dom树再变更

## 述防抖和节流优化
* 节流（throttle）
所谓节流，就是指连续触发事件但是在 n 秒中只执行一次函数。节流会稀释函数的执行频率。
*  防抖（debounce）
所谓防抖，就是指触发事件后在 n 秒内函数只能执行一次，如果在 n 秒内又触发了事件，则会重新计算函数执行时间。
>通俗点讲，就是：
>防抖：
>当我们需要执行某一动作的时候，这个动作可能会重复多次执行，这样的结果可能会要卡死了，严重影响性能，防抖就是当我们频繁触发,直到最后一个触发动作完成之后的第n秒才执行一次。
>节流：
>就是比如一个水龙头，我们控制他的流水速度，不让他一直开最大去流水用来节约资源

## 简述ES6新特性 
* 块级作用域,
* 变量不提升,
* let, const,
* 箭头函数,
* 模板字符串,
* promise,
* async

## 简述箭头函数特性
*  函数体内的this值，绑定的定义时所在的作用域的this
*  不可以当作构造函数
*  不可以使用arguments对象

## webpack打包如何优化
* 定位体积大的模块
* 提取公共模块
* 移除不必要的文件
* 模块化引入
* 通过CDN引用
* 通过 DLLPlugin 和 DLLReferencePlugin 拆分文件
* 开启Gzip压缩
* 压缩混淆代码
* 