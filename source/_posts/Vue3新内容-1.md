---
title: Vue3新内容-1
date: 2020-09-18 13:43:12
tags:
 - Vue
---
尝试使用Vue3的新语法-1，新的特性
<!--more-->
{% raw %}
<script src="https://cdn.jsdelivr.net/npm/vue@3.0.0-rc.12/dist/vue.global.min.js"></script>
{% endraw %}

### Computed计算属性的set和get

```html
<div id="app1">
    <set-computed />
</div>
<script>
  const app1 = Vue.createApp({})
  app1.component('SetComputed',
    {
      data() {
        return {
          num1: 0,
          num2: 2
        }
      },
      computed: {
        val: {
          get() {// get方法，和Vue2中的计算属性一致
            return this.num1 - this.num2;
          },
          set(data) {// set方法，当使用this.val=xxx时,xxx会传递给data，进行一系列的操作
            const arr = data.split('-');
            this.num1 = Number.parseInt(arr[0]);
            this.num2 = Number.parseInt(arr[1]);
          }
        }
      },
      template: `
      <div>{{val}}</div>
      <button @click='change'>change</button>`
      ,
      methods: {
        change() {
          this.val = '9-1';
        }
      }
    })
  app1.mount('#app1')
</script>
```

代码示例

***
{% raw %}
<div id="app1">
    <set-computed />
</div>
<script>
  const app1 = Vue.createApp({})
  app1.component('SetComputed',
    {
      data() {
        return {
          num1: 0,
          num2: 2
        }
      },
      computed: {
        val: {
          get() {
            return this.num1 - this.num2;
          },
          set(data) {
            const arr = data.split('-');
            this.num1 = Number.parseInt(arr[0]);
            this.num2 = Number.parseInt(arr[1]);
          }
        }
      },
      template: `
      <div>{{val}}</div>
      <button @click='change'>change</button>`
      ,
      methods: {
        change() {
          this.val = '9-1';
        }
      }
    })
  app1.mount('#app1')
</script>
{% endraw %}
***

### Teleport透传模板

如果要显示一个对话框，通常要在DOM底部增加一个modal元素，因为不需要处理上下的dom结构，比较方便。Teleport提供了一个方法让组件能将一个html片段注入到Vue控制范围之外的地方。

```html
<div id="app2">
    <teleport-demo></teleport-demo>
  </div>
  <!-- 预留的底部容器 -->
<footer id="app2-foot-wrapper"></footer>

<script>
  const app2 = Vue.createApp({})
  app2.component('TeleportDemo',
    {
      data() {
        return {show: false,data:'我是对话框内容!!!!'}
      },
      methods:
      {
        change(){
          this.show = !this.show;}
      },
      template :
      `
      <div>
        <button @click='change'>显示下面的透传</button>
        <teleport to="#app2-foot-wrapper">
          <div v-if="show">
            <div>
              {{data}}
            </div>
          </div>
        </teleport>
      </div>`
    })
  app2.mount('#app2')
</script>
```

>注意：只能注入到Vue实例控制之外的地方，**wrapper放在实例内就无法注入**

>尽管被注入到了Vue实例控制之外的地方，teleport中的内容仍然隶属于其父组件，如果注入的内容是另一个组件，父组件可以向它传props

***
示例:红框是预留了用来注入的，不包含在Vue实例中
{% raw %}
<div id="app2">
    <teleport-demo></teleport-demo>
  </div>
  <!-- 预留的底部容器 -->
<footer id="app2-foot-wrapper"></footer>
<style>
  #app2-foot-wrapper{
    border: 1px solid red;
  }
</style>
<script>
  const app2 = Vue.createApp({})
  app2.component('TeleportDemo',
    {
      data() {
        return {show: false,data:'我是对话框内容!!!!'}
      },
      methods:
      {
        change(){
          this.show = !this.show;}
      },
      template :
      `
      <div>
        <button @click='change'>显示下面的透传</button>
        <teleport to="#app2-foot-wrapper">
          <div v-if="show">
            <div>
              {{data}}
            </div>
          </div>
        </teleport>
      </div>`
    })
  app2.mount('#app2')
</script>
{% endraw %}

***

### 片段(多根组件)

在Vue2.x中，所有的组件都必须有一个根组件，这样在使用这个组件的时候将所有的属性合并应用到根组件上去，尤其是一些侦听器/class/id/style之类的属性，都是作用在组件的根组件上的。

```html
<template>
  <div>
    <header>...</header>
    <main>...</main>
    <footer>...</footer>
  </div>
</template>
```

在Vue3.0中，一个组件可以不必有根组件（这时称这种组件为片段），但是在这种情况发生时，必须将传过来的属性绑定在一个指定的元素上

```html
<!-- 直接上示例 -->
<div id="app3">
  <fragment-demo class="app3Red" />
</div>
<style>
  .app3Red{
    color: red;
  }
</style>
<script>
  const app3 = Vue.createApp({})
  app3.component('FragmentDemo', {
    template:
    `
    <p>
   <span>第1段</span>
  </p>
  <p v-bind="$attrs">
    <span>第2段</span>
  </p>
  <p>
    <span>第3段</span>
  </p>`,
  })
  app3.mount('#app3')
</script>
```

看下面的示例，由于手动将$attrs绑定到了第2段上，因此，只有第2段的dom上有class="app3Red"的这个属性并且变红

***

{% raw %}
<div id="app3">
  <fragment-demo class="app3Red" />
</div>
<style>
  .app3Red{
    color: red;
  }
</style>
<script>
  const app3 = Vue.createApp({})
  app3.component('FragmentDemo', {
    template:
    `
    <p>
   <span>第1段</span>
  </p>
  <p v-bind="$attrs">
    <span>第2段</span>
  </p>
  <p>
    <span>第3段</span>
  </p>`,
  })
  app3.mount('#app3')
</script>
{% endraw %}

***

### 自定义事件的传值验证

类似[prop的传值验证](https://cn.vuejs.org/v2/guide/components-props.html#Prop-%E9%AA%8C%E8%AF%81),自定义事件的参数也支持传值验证发出警告（无法阻止事件发生）
将自定义事件写在子组件的emits字段中作为一个函数，这个函数就是验证函数

```html
<div id="app4">
    <father>
      <son/>
    </father>
</div>
<script>
  const app4 = Vue.createApp({})
  app4.component('Father', {
    data() {
      return {
        value: 'app'
      }
    },
    methods: {
      getStr(str) {
        this.value = str
      }
    },
    template:
      `<div>
        <div>我只愿意接受子组件的以\'a\'开头的字符串:{{value}}</div>
        <Son @my-event="getStr" />
    </div>
    `
  })
  app4.component('Son', {
    emits: { // 验证函数全都写在这里面

      'my-event': (str) => {
        const pattern = /^a/
        if (!pattern.test(str)) {
          alert('warning')
          return false;
        }
        return true;
      }
    },

    data() {
      return {
        value: '',
      }
    },
    methods: {
      send() {
        this.$emit('my-event', this.value)
      }
    },
    template:
      `<div>
          <input v-model="value" /><button @click="send">发送</button>
    </div>`
  })
  app4.mount('#app4')
</script>
```

***

{% raw %}
<div id="app4">
    <father>
      <son/>
    </father>
</div>
<script>
  const app4 = Vue.createApp({})
  app4.component('Father', {
    data() {
      return {
        value: 'app'
      }
    },
    methods: {
      getStr(str) {
        this.value = str
      }
    },
    template:
      `<div>
        <div>我只愿意接受子组件的以\'a\'开头的字符串:{{value}}</div>
        <Son @my-event="getStr" />
    </div>
    `
  })
  app4.component('Son', {
    emits: { // 验证函数

      'my-event': (str) => {
        const pattern = /^a/
        if (!pattern.test(str)) {
          alert('warning')
          return false;
        }
        return true;
      }
    },

    data() {
      return {
        value: '',
      }
    },
    methods: {
      send() {
        this.$emit('my-event', this.value)
      }
    },
    template:
      `<div>
          <input v-model="value" /><button @click="send">发送</button>
    </div>`
  })
  app4.mount('#app4')
</script>
{% endraw %}

***

>思考：上面这个例子，如果要阻止事件的发生，是否可以在验证函数中再触发一个$emit('prevent-event'),并在父组件监听设置一个标志，如果标志为false，那么就取消消息的接收

>不行，在验证函数中，this指向了window，如果要阻止发生只能将验证的流程写在父组件的处理函数中
