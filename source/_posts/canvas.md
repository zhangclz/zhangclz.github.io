---
title: canvas
date: 2022-03-09
tags: 语法
categories: JS
description: canvas最基本的使用方法
---
以在**微信小程序**给一张图片添加时间水印为例子，介绍两种方法。在web端的用法也基本一样
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