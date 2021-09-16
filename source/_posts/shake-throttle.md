---
title: 函数防抖和节流
date: 2021-09-16
tags: 方法
categories: JS
description: 函数的防抖和节流原理和简单的实现方法
---
### 防抖
函数防抖是事件在触发后隔一段指定时间后才会执行对应函数，期间重复触发事件会重置计时时间
```javascript
// 防抖函数
function shake(fn, timeout = 2000) {
  if(!fn || typeof(fn) != "function") {
    throw new Error("请传入函数")
  }
  let timer = null    //利用闭包，让return出来的函数长期持有timer变量并给timer初始值
  return function(){
    if(timer) {
      clearTimeout(timer)
    }
    timer = setTimeout(() => { fn.apply(this,arguments) }, timeout);
  }
}

// 需要执行的函数
let fun = function(){
  console.log("函数执行了")
}

// 调用防抖函数执行函数，这里我遇到一个坑
// 1. 正确使用
// HTML
<button class="btn"></button>
// JS
let btn = document.querySelector(".btn")
btn.onclick = shake(fun,1000)     //设置点击1秒后执行函数，延时不传默认是2秒

// 2. 踩坑
// HTML
<button onclick="shake(fun,1000)"></button>
// 直接在标签上绑定事件防抖效果不会生效，因为这种方式仅仅是绑定了shake这个函数到点击事件上，
// 并没有去执行这个函数，点击按钮才会去执行shake函数，此时函数又返回了一个函数回来，
// 就导致真正需要执行的函数fun没有被执行
// 而且直接在标签上绑定事件没有办法让函数先执行一次，所以这种方法行不通

// 而第一种方法给元素的点击事件绑定的函数直接就是shake函数执行后的返回值，即fun函数，所以当点击
// 按钮时，执行的是fun函数
```
### 节流
函数节流是触发事件执行函数后，在指定的一段时间内不能再通过触发事件来执行这个函数
```javascript
// 节流函数
function throttle(fn, timeout = 2000) {
  if(!fn || typeof(fn) != "function") {
    throw new Error("请传入函数")
  }
  let timer = null
  return function(){
    if (!timer) {
      fn.apply(this,arguments)
      timer = setTimeout(() => { 
        timer = null
      }, timeout);
    }
  }
}

// 需要执行节流的函数
let realFn = function(){
  console.log("点击了");
}

// 调用节流函数去执行函数
btn.onclick = throttle(realFn,3000)


// 节流函数实现方式有很多，另一种不用定时器，使用时间戳
  let _lastTime = null
  return function () {
    let _nowTime = + new Date()
    if (_nowTime - _lastTime > timeout || !_lastTime) {
        fn.apply(this, arguments)
        _lastTime = _nowTime
    }
}
```
---