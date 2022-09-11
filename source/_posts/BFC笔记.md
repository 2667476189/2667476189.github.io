---
title: BFC笔记
date: 2020-08-28 09:14:01
tags: 
  - CSS
---
BFC又称块级格式化上下文，可以用来创建一个内部元素不会影响外部元素的封闭区域
<!--more-->

## BFC解决的问题

当没有进行BFC处理的时候，父元素内部子元素的变化可能会影响页面其他部分的布局，见下面的示例
***
{% raw %}
<div style="overflow: hidden;">
  <label for="overflow">点击切换父元素overflow:hidden<input id="overflow" type="checkbox" /></label>
  <div class="warp">
    &nbsp;
    <div class="son" style=" background-color: aqua;"></div>
    <div class="son" style="background-color: red;"></div>
  </div>
  <div>anonther DIV</div>
</div>
<script>
  let check = document.querySelector('#overflow')
  check.checked = false
  let warp = document.querySelector('.warp')
  check.addEventListener('click',()=>{
    warp.style.overflow = warp.style.overflow === 'hidden' ? 'visible' : 'hidden'
  })
</script>
<style>
  .warp {
    width: 400px;
    background-color: chartreuse;
  }

  .son {
    margin: 10px;
    float: left;
    background-color: aqua;
    width: 33%;
    height: 200px;
  }
</style>
{% endraw %}
***

如果没有设置父元素**overflow**的值为除了**visiable**的其他值，不触发BFC，那么全部浮动的子元素会使得父元素高度塌陷，后面的布局就会被缩到上面去，也就是子元素的浮动使得其他后续布局被打乱。BFC以后，子元素再怎么设置，外部也不会受到布局被打乱的影响

***
{% raw %}
<div style="overflow: hidden;">
    <label for="isBFC-1">右栏是否BFC<input type="checkbox" id="isBFC-1"></label>
    <button id="add-1">增加右栏文字</button>
    <div class="father-1">
      <div class="son-1">Lorem ipsum dolor sit amet consectetur adipisicing elit. Accusantium nulla aliquam, laboriosam
        mollitia magnam assumenda dolorum ducimus harum excepturi illo culpa, voluptates expedita quisquam omnis
        exercitationem, iste voluptatum inventore veritatis.</div>
      <div class="son-2"> Lorem ipsum dolor sit amet consectetur adipisicing elit. Asperiores, commodi quia! Earum
        repellat ipsam accusantium facere alias sequi, pariatur itaque modi eum atque nemo id consequatur perspiciatis
        corporis voluptatum assumenda.</div>
    </div>
  </div>
  <script>
  let check_1 = document.querySelector('#isBFC-1')
  let add_1 = document.querySelector('#add-1')
  let son_2 = document.querySelector('.son-2')
  check_1.checked = false
  add_1.addEventListener('click', e => {
    son_2.textContent += 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Asperiores, commodi quia! Earumrepellat ipsam accusantium facere alias sequi, pariatur itaque modi eum atque nemo id consequatur perspiciatiscorporis voluptatum assumenda'
  })
  check_1.addEventListener('click',e=>{
    son_2.style.overflow = son_2.style.overflow === 'hidden' ? 'visible' : 'hidden'
  })
</script>
<style>
  .father-1 {
    border: 1px solid red;
  }

  .son-1 {
    float: left;
    width: 200px;
    background-color: bisque;
  }

  .son-2 {
    background-color: cornflowerblue;
  }
</style>
{% endraw %}
***
如果利用浮动实现两栏布局，右侧的文字不触发BFC，那么超出左侧高度后触发环绕效果，失去两栏布局。为右侧栏触发一下BFC，就形成了一个独立的容器，避免了环绕。

## 触发方式

在容器元素上满足以下一项(有时浮动等可能会影响整体布局，因此要选择合适的方法触发BFC)时触发

1. 根元素
2. float属性不为none
3. position为absolute或fixed
4. display为inline-block, table-cell, table-caption, flex, inline-flex
5. overflow不为visible

## BFC会触发的运算规则

1. 内部的Box会在垂直方向，一个接一个地放置。
2. Box垂直方向的距离由margin决定。属于同一个BFC的两个相邻Box的margin会发生重叠
3. 每个元素的margin box的左边， 与包含块border box的左边相接触(对于从左往右的格式化，否则相反)。即使存在浮动也是如此。
4. BFC的区域不会与float box重叠。
5. BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。反之也如此。
6. 计算BFC的高度时，浮动元素也参与计算

> 参考资料
> [W3C-CSS2标准原版](https://www.w3.org/TR/2011/REC-CSS2-20110607/visuren.html#block-formatting)
> [W3C-CSS2标准中文翻译版](http://www.ayqy.net/doc/css2-1/visuren.html#block-formatting)
> [动画教程-如何快速理解BFC](https://www.bilibili.com/video/BV16b411H7Pz)
