---
title: css的inline-block基线问题
date: 2020-10-23 12:53:13
tags:
  - CSS
---

布局的时候遇到一个第二个子元素影响第一个子元素的布局问题，这个是由inline-block的基线对齐导致的。
<!-- more -->

## 遇到的问题

这里直接亮出遇到的问题，父元素为``block``，两个子元素均为``inline-block``，两个盒子在文字行数不同时出现会出现奇怪的行高不对齐现象

```html
<div class="case1">
  <section class="case1-inner case1-a"></section>
  <section class="case1-inner case1-b"></section>
</div>
```

```css
.case1 {
  border: 1px solid red;
}
.case1-inner {
  display: inline-block;
  /*为了方便显示添加以下样式*/
  width: 200px;
  min-height: 150px;
  margin: 5px;
  border: 1px dotted darkcyan;
}
.case1-inner:nth-child(2){
    height:200px;
}
```

---

{% raw %}
  <div class="case1">
    <section class="case1-inner case1-a"></section>
    <section class="case1-inner case1-b"></section>
  </div>
  <button class="case1-btnA">增加a的文字</button>
  <button class="case1-btnB">增加b的文字</button>
  <button class="case1-btn-clear">清除文字</button>
  <script>
  const str = 'Loremipsumdolor';
  const case1A = document.querySelector('.case1-a');
  const case1B = document.querySelector('.case1-b');
  function add(el) {
    return () => {
      el.innerHTML += str;
    }
  }
  document.querySelector('.case1-btnA').addEventListener("click", add(case1A));
  document.querySelector('.case1-btnB').addEventListener("click", add(case1B));
  document.querySelector('.case1-btn-clear').addEventListener('click', () => { case1A.innerHTML = ''; case1B.innerHTML = ''; })
</script>
<style>
  .case1 {
    border: 1px solid red;
  }
  .case1-inner{
    display: inline-block;
    /*为了方便显示添加以下样式*/
    width: 200px;
    min-height: 150px;
    margin: 5px;
    border: 1px dotted darkcyan;
  }
  .case1-inner:nth-child(2){
    height:200px;
  }
</style>
{% endraw %}

---

## 发现的规律

如果多玩几下上面这两个inline-box盒子，会发现他们的对齐方式是有规律的。解决这个问题的关键点在于``vertical-align``，inline-box在多个盒子平放时，如果这几个盒子没能放慢他们的容器，就会需要对齐。默认的``vertical-align``的值是``baseline``基线对齐。在上面的例子中你会发现两个盒子基线的位置如下决定：

1. 如果一个有内容，一个无内容，那么基线就是无内容盒子的bottom，有内容盒子需要调整位置让自己的最后一行文字的基线对齐无内容盒子的bottom。
2. 如果两个都没有内容，那么矮的盒子会沉底，也就是两个盒子底部对齐。
3. 如果两个盒子都有内容，那么基线就是比较高的那个盒子的最后一行的基线，另一个比较矮的盒子将自己最后一行和基线对齐

## 解决这种对不齐问题的方法

在两个盒子中任意一个盒子上加上``vertical-align:[top|bottom]``即可向上或向下对齐，点击下面的按钮给上面的例子中的第一个盒子加上``vertical-align:top``即可查看效果

---
<button class="case2-btn">点击我</button>
<script>
document.querySelector('.case2-btn').addEventListener('click',()=>{
  case1A.style.verticalAlign = "top";
})
</script>
---
