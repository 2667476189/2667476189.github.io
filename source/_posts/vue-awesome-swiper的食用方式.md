---
title: vue-awesome-swiper的食用方式
date: 2020-09-04 13:11:27
tags: 
  - Vue
---

2020年9月4日记vue-awesome-swiper能正常使用的版本及使用方式
<!--more-->

## 下载版本

5.3.6版的swiper和4.1.0版本的vue-awesome-swiper配合可以正常使用

```cmd
npm install swiper@5.3.6 vue-awesome-swiper@4.1.0 --save
```

## 全局引入

在main.js中，添加如下内容

```js
import VueAwesomeSwiper from "vue-awesome-swiper";
import "swiper/css/swiper.css";
Vue.use(VueAwesomeSwiper);
```

## 具体使用

在.vue文件中，使用

```js
import { Swiper, SwiperSlide } from "vue-awesome-swiper";//如有其它组件需要可以再加
```

引入组件，随后便可使用

## 细节配置

在.vue文件具体使用swiper时需要进行参数的调整配置，只要在data(){}中return一个对象，将这个对象以:option="swiperoption"的方式放入swiper组件的attribute中即可，下面给出一个示例

```html
<template>
  <swiper class="swiper" :options="swiperOption">
    <swiper-slide v-for="(i,index) in pic" :key="index">
      <img data-swiper-parallax-scale="0.4" :src="i" alt="图片" />
      <div data-swiper-parallax-opacity="0.3">Slide {{index}}</div>
    </swiper-slide>
  </swiper>
</template>

<script>
export default {
  name: 'mobile',
  data () {
    return {
      swiperOption: {
        /* 一般选项 */
        loop: true,//是否循环
        observer: true,//必加
        observeParents: true,//必加
        direction: 'horizontal',//轮播方向
        initialSlide: 1,//初始slide是第几个
        speed: 1000,//停止滑动slide后贴合边缘的速度
        grabCursor: true,//添加小手图标提醒pc用户可拖拽
        parallax: true,//单个slide会有多个元素，多个元素切换时效果不同产生层次感时开启并在对应元素上添加属性
        on: {//事件注册

        },
        /* 网格布局
        centeredSlides:true,
        slidesPerView:3,
        slidesPerGroup:3,
        */
        effect: 'cube',
        autoplay: {
          delay: 1000,
          disableOnInteraction: true
        }
      },
      pic: [...Array(9)].map((_, i) => 'https://ss2.bdstatic.com/70cFvnSh_Q1YnxGkpoWK1HF6hhy/it/u=1925088662,1336364220&fm=26&gp=0.jpg')
    }
  },
  created () {
  }
}
</script>
<style scoped>
.swiper {
  border: 1px red solid;
  width: 50%;
  height: 500px;
  /* background-image: url('https://ss2.bdstatic.com/70cFvnSh_Q1YnxGkpoWK1HF6hhy/it/u=2636884907,2006278489&fm=26&gp=0.jpg'); */
}
</style>
```

> [Swiper官网的配置文档](https://www.swiper.com.cn/api/index.html)
> [vue-awesome-swiper的示例页面](https://github.surmon.me/vue-awesome-swiper/)
