---
title: 毕业设计中ElementUI的问题
comments: true
tags:
  - ElementUI
  - Vue
  - 毕业设计
date: 2020-04-12 12:35:25
categories: Vue
---
[项目地址](https://github.com/Zooeeee/Online_exam)
### 记录在毕业设计 在线考试系统 中使用element ui的问题
1. 编辑每条记录，却使用一个dialog，会造成数据不更新
此时在父组件dialog的子组件中动态绑定一个key值，利用vue的diff算法，可以实现重新挂载
2. 场景：编辑某个数据，在想给下拉选择多选框赋值时，一开始以为就是把赋值反过来做，多选框里始终现实的时value而不是label。最后试出来应该在倒推赋值的时候用map将数组中的每个值从字符串变成int
3. 
   * 场景：在使用组件时，需要对样式进行修改，却无法做到
   * 使用less组件穿透修改样式
   *  代码：
```less
.wrapper /deep/ .swiper-pagination-bullet-active{
  background: #fff;
}
```
4. 动态添加v-model 
* 场景：在生成试卷时，因为每次考试的试卷题目都是动态生成的，无法写死v-model
* 需求：动态添加vmodel 
核心
* 创建一个空的数组
```JS
selectVal: []
```
* v-model 绑定
```JS
v-model="selectVal[index].value"
```
* 动态绑定
```JS
var len = 10;
for (var i = 0; i < len; i++) {
    var item = {value: ''};
    this.selectVal.push(item);
}
```
5. 组件刷新 场景:在dialog提供记录的crud之后,需要刷新网页的状态
   * 通过vuex的计算属性(最好的方法,不会出现页面闪烁)
   * this.$router.go(0)
   * this.$forceUpdate()
   * 在app组件的路由界面 通过控制v-if的方法刷新
```html
 <router-view v-if="isRouterAlive"></router-view>
```
```js
export default {
  // app容器提供一个方法 用于刷新页面
  name: 'App',
  provide () {
  // 父组件中通过provide来提供变量，在子组件中通过inject来注入变量。
    return {
      reload: this.reload
    }
  },
  data () {
    return {
      isRouterAlive: true// 控制视图是否显示的变量
    }
  },
  methods: {
    reload () {
      this.isRouterAlive = false// 先关闭，
      this.$nextTick(function () {
        this.isRouterAlive = true// 再打开
      })
    }

  }
}
// 在子组件中通过注入调用reload方法
 inject: ['reload'],
```