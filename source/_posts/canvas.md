---
title: canvas
date: 2022-03-09
tags: 语法
categories: JS
description: canvas基本的使用方法
---
以在**微信小程序**给一张图片添加时间水印为例子，介绍两种方法
1. CanvasContext：canvas旧版的接口
直接上代码
```HTML
<!-- 获取canvas上下文需要canvas-id这个属性 -->
 <canvas id='canvas' canvas-id="firstCanvas"></canvas>
```
```javascript
let time = new Date()
// 创建canvas组件绘图上下文
let ctx = wx.createCanvasContext("firstCanvas");
// 将图片绘制到画布上，从图片（0，0）坐标开始（左上角），绘制的大小为图片的实际大小
ctx.drawImage(imgPath, 0, 0, imgW, imgH);

// 注意，顺序是要先设置文字的属性再绘制文字
ctx.setFontSize(8);   // 设置文字大小
ctx.setFillStyle("white");    // 设置文字颜色
ctx.fillText(time, 10, 10);   // 绘制文字，从坐标（10，10）处开始

// 最后将画布内容绘制到canvas上并保存成图片
ctx.draw(false, ()=>{   // false是不保留上次的绘制结果，用新的模板进行绘制。设置定时器是为了保证绘制完成才执行回调去合成图片
  setTimeout(()=>{
    wx.canvasToTempFilePath({
       canvasId: "firstCanvas",
       success: (res) => {
         console.log(res.tempFilePath)
       },
       fail(err){
         console.log('合成失败')
     })
  },500)
})
```
2. 使用canvas 2D，canvas的新版接口
```HTML
<!-- 获取canvas上下使用id或calss这个属性，并且必须指定type -->
 <canvas id='canvas' type='2d'></canvas>
```
```javascript
let time = new Date()
const query = wx.createSelectorQuery()  //创建一个查询，用于微信小程序获取页面元素
query.select('#canvas')
 .fields({ node: true, size: true })
 .exec((res) => {
   const canvas = res[0].node   //页面元素
   const ctx = canvas.getContext('2d')
   let image = canvas.createImage()   //需要创建一个图片对象才能对图片进行操作
   image.src = imgPath
   image.onload = () => {
     ctx.drawImage(image, 0, 0, imgW,imgH)
     ctx.font="18px sans-serif"
     ctx.fillStyle="#fff"
     ctx.fillText(time, 20, 30);
    //  canvas 2D绘制完成图片后，只能把图片转成base64格式的数据流，此时使用img标签就能正常显示了
     let result = canvas.toDataURL()
   }
 })
```

**web端**
1. web端使用canvas 2D给图片添加水印，和小程序有些许不同
```html
<!-- 画布大小先不设置，后面根据图片大小来动态设置，可以先把画布定位到屏幕外面去 -->
  <canvas id='canvas' type='2d'></canvas>
  <!-- 图片可以先设置大小让他在网面上占位 -->
  <img class="imgWrap" mode="widthFix" width="600px">
```
```javascript
  let time = new Date().toLocaleDateString()
  let imgWrap = document.querySelector(".imgWrap")
  const canvas = document.querySelector("#canvas")
  const ctx = canvas.getContext('2d')
  // web端处理图片必须先生成一个图片对象
  let image = new Image()
  image.src = "图片的本地路径"
  // 必须在onload函数中才能获取到图片并在它上面绘图
  image.onload = function(){
    // 画布大小设置和图片一样大，图片质量就不会改变，也不会被裁剪
    canvas.width = image.width
    canvas.height = image.height
    // 字体根据图片大小动态设置
    let fontS = image.width/25
    // 将图片画到画布上
    ctx.drawImage(image,0,0)

    ctx.fillStyle="#0000ff"
    ctx.font = `${fontS}px Arial` // 设置字体也必须指定字体类型
    ctx.fillText(time,50,100)
    // canvas 2D只能把图片转换为base64格式，直接用img标签即可显示
    let res = canvas.toDataURL()
    imgWrap.setAttribute("src",res)
  }
```

2. 将网页导出为pdf，并添加水印
  实现思路：
  * 添加水印
   原理：创建一个元素，使用绝对定位或固定定位放到页面的最上层（设置最高层级），css禁用鼠标事件（`pointerEvents: none`）
   然后就根据实际需要设置 css 样式，控制水印的数量等...
  * 导出为 pdf
   使用第三方插件：**jspdf 和 html2canvas**
   先使用 html2canvas 插件将网页转换为画布，然后创建一个图片，将画布的内容再转换成图片，最后使用 jspdf 插件将图片转为 pdf 导出
   html2canvas 插件无法精确处理伪元素，如果使用了伪元素，样式可能会有问题，因此转换为画布的时候，要先处理一下伪元素，建议是不使用伪元素。如果使用了，则可以在转换前，复制一份 dom 元素，然后用其他元素替换伪元素的效果，再将复制的 dom 元素添加到真实 dom 中，然后将复制的元素导出为 pdf，再从真实的 dom 中移除这个复制的 dom。如果样式一开始就没问题，则直接用原 dom 导出即可
   
```javascript
// 部分代码
html2canvas(container).then(canvas => {
  let context = canvas.getContext("2d");
  context.mozImageSmoothingEnabled = false;
  context.webkitImageSmoothingEnabled = false;
  context.msImageSmoothingEnabled = false;
  context.imageSmoothingEnabled = false;
  const imgData = canvas.toDataURL("image/png", 1.0);
  let img = new Image();
  img.src = imgData;

  img.onload = function() {
    var filename = "xxx.pdf";
    // 获取dom高度、宽度
    img.width = img.width / 2;
    img.height = img.height / 2;
    img.style.transform = "scale(0.5)";
    let pdf = null;
    if (width > height) {
      // 此可以根据打印的大小进行自动调节
      pdf = new jsPDF("l", "mm", [width * 0.5, height * 0.5]);
    } else {
      pdf = new jsPDF("p", "mm", [width * 0.5, height * 0.5]);
    }
    pdf.addImage(imgData, "jpeg", 0, 0, width * 0.5, height * 0.5);
    pdf.save(filename);
  };
});
```