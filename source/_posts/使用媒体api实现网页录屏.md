---
title: 使用媒体api实现网页录屏
date: 2021-11-13 20:44:35
tags:
  - JavaScript
  - 尝鲜
---

使用`MediaDevices`中提供的接口实现前端进行网页的录屏，并实时转化为二进制数据（视频）

<!-- more -->

## 使用`MediaDevices.getDisplayMedia`获取屏幕

通过`MediaDevices.getDisplayMedia`，可以在征得用户允许的情况下，获得窗口或者整个屏幕的录制权限（取决于用户的选择），其使用promise风格的方式进行用户权限获取

```js
async () => {
  try{
    stream = await MediaDevices.getDisplayMedia({
      video:true
    })
  }catch(err){
    console.log(err)
  }
  
}
```

该函数接收一个称作`constraints`的参数，即可以利用此参数配置一些类似采样率之类的专业的音视频参数，但是支持度可能存在一定的问题

## 使用获取到的视频流

使用`MediaRecorder`来进行流的处理，可以进行实时的包装，停止录屏后将记录到的流打包成为二进制大对象(blob)并置入页面的视频源中，也可以利用页面媒体元素中的`srcObject`进行更简单的操作

```js
// 通过MediaRecorder进行录制重放

// 检测指定的mime类型能否被MediaRecorder录制
const mimeType = MediaRecorder.isTypeSupported('video/webm; codecs=vp8')
? 'video/webm; codecs=vp9'
: 'video/webm'
const recorder = new MediaRecorder(stream,{mimeType})
let chulks = []
// 为recorder绑定事件，处理组装数据和流停止的后续处理
recorder.addEventListener('dataavailable',e=>chulks.push(e.data))
recorder.addEventListener('stop',_=>{
  // 二进制大对象的处理，将chulks转为内存映射的blob地址
  const blob = new Blob(chulks,{
    type: chulks[0].type
  })
  // 将blob挂载到video元素上
  video.src = URL.createObjectURL(blob)
})
recorder.start()
```

## 更简易的方式

使用实验性的`srcObject`，可以直接将视频流绑定到页面的多媒体元素上

```js
video.srcObject = stream
```

## 实例

{%raw%}
<fieldset>
  <legend>停止后再观看</legend>
  <video class="video" width="600px" controls></video>
</fieldset>
<fieldset>
  <legend>实时录屏的video元素</legend>
  <video class="video2" width="600px" autoplay></video>
</fieldset>
<button class="record-btn">开始录制</button>
<script>
const recordBtn = document.querySelector('.record-btn')
const video = document.querySelector('.video')
const video2 = document.querySelector('.video2')
recordBtn.addEventListener('click',async ()=>{
  const stream = await navigator.mediaDevices.getDisplayMedia({
    video:true,
    audio:true
  })
  video2.srcObject = stream

  const mimeType = MediaRecorder.isTypeSupported('video/webm; codecs=vp8')
  ? 'video/webm; codecs=vp8'
  : 'video/webm'
  const recorder = new MediaRecorder(stream,{mimeType})
  let chulks = []
  recorder.addEventListener('dataavailable',e=>chulks.push(e.data))
  recorder.addEventListener('stop',_=>{
    const blob = new Blob(chulks,{
      type: chulks[0].type
    })
    video.src = URL.createObjectURL(blob)
  })
  recorder.start()
})
</script>
{%endraw%}

## 总结

在[Chrome Dev Submit 2021](https://youtu.be/Df2U9-R-OJs)的线上宣讲会中，邀请到了[kapwing](https://www.kapwing.com/)来分享他们如何利用最新的web多媒体api搭建在线多媒体编辑器

正在使用的Api

| Api                | 实现的功能                                           |
| ------------------ | ---------------------------------------------------- |
| IndexedDB          | 尽可能将数据存储在本地以支持高性能的编辑             |
| Web Sockets Api    | 允许用户进行实时协作                                 |
| Web Audio Api      | 加载音轨，实现对声音的编辑操作                       |
| Media Recorder Api | 记录网络摄像头和屏幕，实现表情记录等功能             |
| Web Workers        | 将复杂的运算分流到workers中，避免影响主线程性能      |
| PWA                | 渐进式webapp，第一次加载完成后，使应用可以安装到桌面 |

未来计划使用的功能

| Api                     | 实现的功能       |
| ----------------------- | ---------------- |
| Web Codecs              | 加速帧绘制       |
| WebGL                   | 更复杂的动画效果 |
| Media Source Extensions | 加速片段间的切换 |

除此之外，大会上还介绍了[基于容器的媒体查询功能](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Container_Queries)，探讨了Google内置隐私沙盒的功能并逐渐限制第三方cookie。谷歌对浏览器的垄断，导致了chrome大步走在浏览器的最前沿，未来还会有哪些各种各样的新奇技术，或许让前端工程师包罗一切的话，头发都不够掉的🤣