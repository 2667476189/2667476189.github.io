---
title: MDN上的在线运行是怎么做的（仅限html+css）
date: 2021-07-16 15:03:22
tags: 
 - html
 - CSS
---

MDN上有许多的教程支持在线运行，其中比较简单的是CSS+html的在线运行，速度很快没有卡顿，这里研究一下它是怎么实现的。

<!-- more -->

## 示意链接

​	在MDN上CSS学习部分的教程区有[这一部分](https://developer.mozilla.org/zh-CN/docs/Learn/CSS/Building_blocks/Cascade_and_inheritance#优先级)，下面有两个编辑区域，第一个section用来显示，下面两个textarea用来分别编辑CSS和html的结构，由于避免影响外部页面它是嵌在iframe里面的。

## 原理

​	其实它实际上就是很简单的一个section，两个textarea和一个style的标签，而这个iframe中核心的playable.js其实只有短短的22行代码，``司马昭之心路人皆知``，这个js也是一目了然，没有什么trick，把它的html和js写在下面

```html
<style class="editable"></style>
<section class="preview">
</section>
<textarea class="playable" style="height: 120px;">
</textarea>
<textarea class="playable" style="height: 120px;">
</textarea>
```

```js
var section = document.querySelector('section');
var editable = document.querySelector('.editable');
var textareaHTML = document.querySelector('.playable-html');
var textareaCSS = document.querySelector('.playable-css');
var reset = document.getElementById('reset');
var htmlCode = textareaHTML.value;
var cssCode = textareaCSS.value;
function fillCode() {
    editable.innerHTML = textareaCSS.value;
    section.innerHTML = textareaHTML.value;
}
reset.addEventListener('click', function () {
    textareaHTML.value = htmlCode;
    textareaCSS.value = cssCode;
    fillCode();
});
textareaHTML.addEventListener('input', fillCode);
textareaCSS.addEventListener('input', fillCode);
window.addEventListener('load', fillCode);
```

## 应用到博客里试一下

  把代码和相关的样式复制过来，就可以了，但是这里没有嵌入iframe，写个*{color:important}这样的样式，就会影响到整个博客的样式，博客的样式也会影响到这个在线运行的本身，所以针对h1或者列表这种博客已经全局赋予的样式会有所影响

{% raw %}
<style>
    /* 这边的样式是让编辑框和预览区域更好看的 */
    .playable {
    font-family: monospace;
    display: block;
    margin-bottom: 10px;
    background-color: #F4F7F8;
    border: none;
    border-left: 6px solid #558ABB;
    color: #4D4E53;
    width: 90%;
    max-width: 700px;
    padding: 10px 10px 0px;
    font-size: 90%;
  }
  .playable-css {
    height: 80px;
  }

  .playable-html {
    height: 160px;
  }

  .playable-buttons {
    text-align: right;
    width: 90%;
    max-width: 700px;
    padding: 5px 10px 5px 26px;
    font-size: 100%;
  }

  .preview {
    width: 90%;
    max-width: 700px;
    min-height: 100px;
    border: 1px solid #4D4E53;
    border-radius: 2px;
    padding: 10px 14px 10px 10px;
    margin-bottom: 10px;
  }

  .preview input {
    display: block;
    margin: 5px;
  }
</style>

<style class="editable"></style>
<div>
<section class="preview">
</section>
<textarea class="playable playable-css" style="height: 120px;">
</textarea>
<textarea class="playable playable-html" style="height: 120px;">
</textarea>
<script defer>
    var section = document.querySelector('section');
    var editable = document.querySelector('.editable');
    var textareaHTML = document.querySelector('.playable-html');
    var textareaCSS = document.querySelector('.playable-css');
    var reset = document.getElementById('reset');
    var htmlCode = textareaHTML.value;
    var cssCode = textareaCSS.value;
    function fillCode() {
        editable.innerHTML = textareaCSS.value;
        section.innerHTML = textareaHTML.value;
    }
    textareaHTML.addEventListener('input', fillCode);
    textareaCSS.addEventListener('input', fillCode);
</script>
{% endraw %}

