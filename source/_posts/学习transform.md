---
title: 学习transform
date: 2020-09-26 09:23:21
tags:
  - CSS
---
CSS3的transform有很丰富的变换类型，可以做出各种效果
<!--more-->

## 转换的查看方法

使用火狐浏览器的控制台可以查看转换前和转换后的状态及对应点的连线，选中转换后的元素，鼠标移动到css中的transform的值上，即可显示如下的转换过程。
![firefox查看转换的示例图片](https://gitee.com/assd12138/cdnpics/raw/master/img/QQ图片20201025144808.png)

## translate

``translate(tx,ty)`` 这个CSS 函数用于移动元素在平面上的位置。接收两个值，分别表示元素在笛卡尔坐标系上进行横向和纵向移动的值，第二个参数不写默认为0。
``translateX(tx)``和``translateY(ty)``都默认另一个方向上的值为0，可以用``translate(tx,ty)``代替

```css
  .case1 {
    height: 100px;
    width: 100px;
    border: 1px solid red;
    transform: translate(12px, 50%);
  }
```

---

{% raw %}
<button class="case1-btn">转变</button>
<div class="case1"></div>
<script>
  const case1Div = document.querySelector('.case1');
  document.querySelector('.case1-btn').addEventListener('click', () => {
    case1Div.style.transform = case1Div.style.transform === 'none' ? 'translate(12px, 50%)' : 'none';
  })
</script>
<style>
  .case1 {
    height: 100px;
    width: 100px;
    border: 1px solid red;
    transform: translate(12px, 50%);
  }
</style>
{% endraw %}

---

## scale

``scale(sx,sy)``用于定义缩放，缩放的方法从盒子的``transform-origin``向两侧/上下缩放，sx是横向缩放的比例，sy是纵向缩放的比例，如果不填sy，那么sy的默认值为sx即等比例缩放。和``translate``类似的，``scale``也有``scaleX``和``scaleY``,但是使用这两个另一个方向上的默认值为1，表示1:1不缩放。

>在Firefox浏览器下，缩放比例为浮点数时，可能会导致边框渲染不正常无法显示([bug1476379](https://bugzilla.mozilla.org/show_bug.cgi?id=1476379)),下面例子中的第二个盒子在firefox中可能无法渲染出部分边框

```css
  .case2 {
    height: 100px;
    width: 100px;
    border: 1px solid red;
    transform: scale(1.2,0.6);
  }
```

---

{% raw %}
  <button style="position: relative; z-index: 2;"" class="case2-btn">转变</button>
  <div class="case2"></div>
  <div class="case2" style="scale: 0.5 0.5;"></div>
  <script>
  const case2Div = document.querySelector('.case2');
  const case2DivDefault = case2Div.style.transform
  document.querySelector('.case2-btn').addEventListener('click', () => {
    case2Div.style.transform = case2Div.style.transform === 'none' ? case2DivDefault : 'none';
  })
  </script>
  <style>
    .case2 {
      height: 100px;
      width: 100px;
      border: 1px solid red;
      transform: scale(1.2,0.6);
    }
  </style>
{% endraw %}

---

## rotate

``rotate(a)``以``transform-origin``为不动点旋转一个元素（可以把整个元素想象成一张非常大的纸上的一块区域，然后按住不动点，转这张纸一定角度，这块区域也会随之旋转）。正值为顺时针，负值为逆时针。

>[CSS角度值的单位](http://caibaojian.com/css3/values/angle/index.htm)

```css
  .case3 {
    height: 100px;
    width: 100px;
    border: 1px solid red;
    transform: rotate(0.3turn);
  }
```

---

{% raw %}
  <button style="position: relative; z-index: 2;"" class="case3-btn">转变</button>
  <div class="case3"></div>
<script>
  const case3Div = document.querySelector('.case3');
  const case3DivDefault = case3Div.style.transform
  document.querySelector('.case3-btn').addEventListener('click', () => {
    case3Div.style.transform = case3Div.style.transform === 'none' ? case3DivDefault : 'none';
  })
</script>
<style>
  .case3 {
    height: 100px;
    width: 100px;
    border: 1px solid red;
    transform: rotate(0.3turn);
  }
</style>
{% endraw %}

---

## skew

倾斜，这个变换比较复杂，可以查看[这篇文章](https://juejin.im/post/6854573212743761934)

## transform-origin

``transform-origin``默认如下

```css
transform-origin:50% 50% 0
```

也就是盒模型的几何中心

>[transform的图形转换原理](https://developer.mozilla.org/zh-CN/docs/Web/CSS/transform-function)
