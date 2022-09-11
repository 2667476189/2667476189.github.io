---
title: Vue响应式的基础原理
date: 2021-11-14 09:19:23
tags:
  - Vue
---

Vue响应式的基础原理

<!-- more -->

## 拦截操作的`Object.defineProperty()`

使用`Object.defineProperty()`可以设置一个对象的成员的`getter`和`setter`以此来拦截对于新成员的操作，目标是实现一个`observe`函数，能够为一个对象的所有属性设置`getter`和`setter`

```js
function observe(obj){
    for(const key in obj){
        // 初始化代理对象，对于obj[key]的后续读写其实都是对于这个代理对象的操作
        let val = obj[key]
        Object.defineProperty(obj,key,{
            get(){
                return val
            },
            set(newVal){
                val = newVal
            }
        })
    }
}
```

## 实现手动式的依赖收集模式

### 什么是依赖

将一个对象上所有的操作会牵连到的所有其他必须再运算的的操作，可以称之为`副作用`，例如

```js
let a = 1
let b = a*3
a = 2
console.log(b) // 3
```

上面这个b在a更改后，不能保持其与a的3倍的同步，为了保持这个同步，需要再次计算一次

```js
let a = 1
let b = a*3
function effect(){
    b = a*3
}
a = 2
effect()
console.log(b) // 6
```

上面的这个`effect`函数就是一个副作用，每次我们调用完给`a`设置值以后，都必须手动调用`effect()`来再次的进行运算，因此这个副作用也就被称作为一个依赖。

### 可优化点

上面写法的不良之处有两点，

1. `effect`中的`a*3`写了两次，初始化中写了一次，又在定义副作用时写了一次。
2. 副作用需要手动运行，每次对a进行更改后，都需要手动的运行`effect()`

为了优化这两个缺点，下面分成两步来进行

### 解决写两次的问题

#### 简单的`autorun`

通过一个函数体来进行定义依赖的次数的节约，假设我们有一个`autorun`函数，只要把所有的副作用代码写在里面，就可以达成只写一次的目标，要实现的效果如下

```js
let a = 1
let b
function effect(){
    b = a*3
}
autorun(effect)
a = 2
effect()
```

那么这个autorun的写法如下

```js
function autorun(fn){
    fn()
}
```

#### 使用`Dep`将`effect`函数收集起来

以上的`autorun`只能够初始化的时候，帮我们运行一下`effect`，为了实现下一步的`effect`自动化触发，我们需要想办法把这个`effect`函数收集起来，这里就引入了依赖收集的概念，目标是实现`Dep`以达成以下的操作

```js
let a = 1
let b
let dep = new Dep()
autorun(()=>{
    // 只要运行这个函数，就能把这个箭头函数作为依赖收集起来
    dep.depend()
    b = a*3
})
a = 2
dep.notify()
```

通过调用`dep.depend()`，能够把自己所处的这个箭头函数作为依赖收集起来，而通过调用`dep.notify()`，就能够通知副作用的再次运行。

首先考虑非箭头函数的实现方式

```js
class Dep{
    constructor(){
        this.subscribers = new Set()
    }
    depend(){
        this.subscribers.add(effect)
    }
    notify(){
        // 重新运行所有依赖
        this.subscribers.forEach(sub=>sub())
    }
}
function autorun(fn){
    fn()
}
const dep = new Dep()
let a = 1
let b
function effect(){
    dep.depend()
    b = a*3
}
autorun(effect)
a = 2
dep.notify()
console.log(b) // 6
```

由于把`effect`写死在了`Dep`内，使得这个类仅能适用于`effect`函数，如果后续有`effect2`，则无法使用，所以为了使`Dep`通用化，需要想办法实现类似最初的箭头函数的写法，并且让`Dep`能够拿到这个箭头函数的运行上下文，使用的技巧就是全局的提取，提取的时机就是在`autorun`中实际要运行`fn`前，通过这种方式，我们就可以拿到箭头函数的实例

```js
let globalFn
class Dep{
    depend(){
        // 当副作用是通过dep.notify调用时，globalFn为null因此不会被收集
        if(globalFn){
            this.subscribers.add(globalFn)
        }
    }
    // ...
}
function autorun(fn){
    globalFn = fn
    fn()
    globalFn = null
}
```

### 解决需要手动调用的问题

通过解决写两次的问题，创建了一个`Dep`类能够收集起来`effect`，并且使用`depend`和`notify`实现依赖的管理。而最终极的写法是将手动调用Dep的代码也给封装起来，实现以下的调用形式

```js
let a = 1
let b
autorun(()=>{
    b = a*3
})
a = 2
console.log(b) // 6
```

为了使其更漂亮，将变量放在一个`state`和`computed`中来使其更类似于`Vue`

```js
let state = {
    a:1
}
let computed = {
    b:undefined
}
autorun(()=>{
    computed.b = state.a * 3
})
state.a = 2
console.log(computed.b) // 6
```

为了使`dep.depend`和`dep.notify`能够被自动调用，通过`observe`中的`getter`和`setter`钩子来勾住这两个调用：在获取变量时，使用`depend`收集依赖；在设置变量时，使用`notify`重新计算依赖

简单响应式系统的实现代码如下

```js
class Dep{
    constructor(){
        this.subs = new Set()
    }
    depend(){
        if(globalFn){
            this.subs.add(globalFn)
        }
    }
    notify(){
        this.subs.forEach(sub=>sub())
    }
}
let globalFn
let dep = new Dep()
function observe(obj){
    for(const key in obj){
        let val = obj[key]
        Object.defineProperty(obj,key,{
            get(){
                dep.depend()
                return val
            },
            set(newVal){
                val = newVal
                dep.notify()
            }
        })
    }
}
function autorun(fn){
    globalFn = fn
    fn()
    globalFn = null
}
```

使用的方法则如下

```js
let state = {
    a:1
}
let computed = {
    b:undefined
}
// 观察state中的所有变量，使其附上dep钩子
observe(state)
autorun(()=>{
    computed.b = state.a * 3
})
state.a = 2
console.log(computed.b) // 6
```

### 进一步使其更像`Vue`

```js
class miniVue{
    constructor(option){
        // 初始化globalFn和dep
        this.observe(option.data)
        this.autoRun(option.computed)
    }
    autoRun(computed){
        // 遍历computed，取出每个函数并运行依赖，
        // 此时dep就会在computed变量和data中架起桥梁，
        // computed依赖于data，而data中的get在调用时正好搭起了dep的桥梁
        for(const fnName in computed){
            this.globalFn = computed[fnName]
            computed[fnName]()
            this.globalFn = null
        }
    }
    observe(){
        // ...
    }
}
```

