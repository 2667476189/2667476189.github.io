---
title: Vue3新内容-2
date: 2020-09-19 15:54:39
tags:
 - Vue
---
尝试使用Vue3的新语法-2，使用setup()
<!--more-->

## 引入setup的原因

类似[React的自定义hooks](https://react.docschina.org/docs/hooks-custom.html),有时在不同的组件中需要共用同一逻辑，为了避免不同的逻辑分散在各个组件之中，可以利用setup抽取出公共逻辑。

以React文档中的例子,抽取一个判断用户是否在线的逻辑组件

```js
import {ref} from 'vue'
import {getUserStatus} from '@/api/user'
export default {
  props:{
    friendID : { type:String } // prop验证
  },
  // setup相当于useEffect
  setup(props){ // props是父组件传过来的参数
    const friendID = toRef(props,'friendID');
    let isOnline = ref(false);// ref相当于useState，用来在setup中创建响应式变量
    const fetchIsOnline = async () = >{
      isOnline = await getUserStatus(friendID);
    };
    onMounted(fetchIsOnline);
    return {
      isOnline
    }
  }
}
```

## ref/reactive与toRef/toRefs

ref和reactive分别用来创建组合型或者是单独写法的响应式变量，但是使用reactive时需要注意返回方式和消费者组件的调用方式

单独式写position的写法如下

```js
// 创建
let x = ref(0);
let y = ref(0);
// 设置
x = 1;
y = 2
// 返回
return {
  x,y
}

// 消费
let {x,y} = usePosition();
```

组合式写法

```js
// 创建
let position = reactive({
  x:0,
  y:0
});
// 设置
position.x = 1;
position.y = 1;
```

针对组合式写法必须注意使用的时候如果要解构不能直接解构，而应该使用toRef/toRefs

返回消费方式1

```js
// 返回
return {
  position
}
// 消费
let position = usePosition();
let {x,y} = toRefs(position);
```

返回消费方式2

```js
// 返回
return {
  position
}
// 消费
let position = usePosition();
let x = toRef(position,'x');// 参数2就是要解构哪个值出来
let y = toRef(position,'y');
```

返回消费方式3

```js
// 返回
return toRefs(position)
// 消费
let position = usePosition();
let {x,y} = toRefs(position);// toRefs相当于一次性toRef所有值
```

错误方式

```js
// 返回
return {
  position
}
// 消费
let {x,y} = usePosition();// 不使用toRef而直接解构会失去响应性
```
