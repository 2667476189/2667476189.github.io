## `composition api`和`option api`的对比 

| option特性                                                   | composition写法                            |
| ------------------------------------------------------------ | ------------------------------------------ |
| data                                                         | ref()，变量取值使用xxx.value               |
| props                                                        | setup的参数传入,setup(props)               |
| 生命周期钩子mounted<br />beforeCreate和created已经不需要（因为此刻的执行时机相当于在setup中执行） | onMounted                                  |
| watch<br />要观测props的变化，需要使用toRefs包裹props<br />并使用xxx.value获取实际props的值 | 函数watch(ref,(newVal,oldVal)=>{},options) |
| computed                                                     | computed(()=>{})                           |

## `option api`中的`setup`回调

1. `props`

在`option`的`props`设置相应的格式，`setup`回调中取得`props`后，可以直接`props.xxx`取得其中的值，也可以使用`toRefs(props)`包裹以后进行解构，然后使用`xxx.value`进行访问；或者使用`toRef(props,key)`进行单个属性的响应式包裹。如果是需要`watch`监听，则必须先使用`toRef`或者`toRefs`进行响应式包裹。

2. `context`

含有`attrs`、`slots`、`emit`、`property`参数

## 模板引用`ref`

在`setup`函数的上下文中声明了与模板中设置的`ref`同样`key`值的`ref`后，在`onxxx`生命周期钩子中，即可得到模板引用的值

```html
<script setup>
import {onMounted} from "vue"
const root = ref(null)
onMounted(()=>{
    console.log(root) // 这里root就有值了
})
</script>
<template>
<div ref="root">    
</div>
</template>
```

## `<script setup>`

| 特性                   | 语法                                                         |
| ---------------------- | ------------------------------------------------------------ |
| props                  | defineProps，[参数](https://v3.cn.vuejs.org/api/options-data.html#props) |
| emit                   | defineEmits，[参数](https://v3.cn.vuejs.org/api/options-data.html#emits) |
| 手动向外部暴露组件参数 | defineExpose                                                 |

## 样式的SFC

主要是vue2经典的scoped单文件组件和新增的module单文件组件（可以提供css in js的功能）

> 由于vue3支持片段，因此使用scoped时，如果组件提供了根元素，则根元素会绑定两个`data-v-xxx`哈希值，而未提供根元素的片段则只会绑定当前组件的的哈希值

具体的直接看(文档)[https://v3.cn.vuejs.org/api/sfc-style.html#style-module
