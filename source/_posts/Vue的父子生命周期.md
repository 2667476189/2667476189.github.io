---
title: Vue的父子生命周期
date: 2020-09-26 07:59:55
tags:
  - Vue
---

Vue组件的生命周期和父子组件的生命周期
<!--more-->

### 简单描述单个组件的生命周期

对于单个组件，其生命周期为beforeCreate->created->beforeMounte->mounted->beforeUpdate->beforeDestory->destroyed
一般最常用的是在created中进行初始化运算和ajax加载操作，在mounted中获取dom

下面用三张图片直接表现单个组件的生命周期，钩子作用和实际作用:
Vue生命周期
![Vue生命周期](https://gitee.com/assd12138/cdnpics/raw/master/img/VueLifeCycle.png)
Vue的钩子作用
![Vue的钩子作用](https://gitee.com/assd12138/cdnpics/raw/master/img/UseageOfLifeCycle.png)
Vue的钩子在实际业务中的作用
![Vue的钩子在实际业务中的作用](https://gitee.com/assd12138/cdnpics/raw/master/img/RealUsage.png)

### 父子组件的生命周期

父子组件的生命周期顺序如下

1. 创建：父组件运行到beforeMount，等待子组件运行到mounted完成，父组件再mounted。
2. 仅父组件更新：父beforeUpdate->父updated
3. 子组件更新：父beforeUpdate->子beforeUpdate->子updated->父updated
4. 销毁：父beforeDestroy->子beforeDestroy->子destroyed->父destroyed

### vm.$nextTick(callback)干啥的

```html
<div id="example">{{message}}</div>
```

```js
let vm = new Vue({
  el: '#example',
  data: {
    message: '123'
  }
})
vm.message = 'new message' // 更改数据
vm.$el.textContent === 'new message' // false
Vue.nextTick(function () {
  vm.$el.textContent === 'new message' // true
})
```

当设置**vm.message = 'new message'**的时候，dom并没有立刻更新，因此立刻去获取dom内容时没法获取到。放在$nextTick中的函数，会在dom更新完以后再运行，此时就可以获取到。
