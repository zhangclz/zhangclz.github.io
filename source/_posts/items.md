---
title: tips
date: 2021-09-15
tags: 概念
categories: 日常
description : 一些实用的概念，骚操作和灵感
---
### JavaScript
* 对字符串直接比较大小会自动把字符串转换为ascll码来比较
数字和数字字符串比较会做隐式转换为数字
数字和非数字字符串比较，永远是false，因为非数字字符串隐式转换为数字会得到NAN
* 数字加字符串会把数字做隐式转换为字符串后再进行拼接，对于数字字符串可先**乘1**或者**除1**再和数字相加
* 字符串也可以像数组一样用下标来获取指定位置的字符
* 直接用`new Date()`获取的时间相减会得到时间戳
* js事件循环机制（event loop）：同步任务直接进入主线程执行，异步任务提交给对应的异步进程进行处理，处理完成之后移入任务队列，当主线程中的任务全部执行完成后，就从任务队列读取一个任务进入主线程执行。重复该动作就称为事件循环
* 数组的`splice()`方法返回值是被删除的值组成的数组，并不是删除之后剩余的数组
* 获取对象的长度，可以先用`Object.entries()`把对象的属性和值转换成了键值对数组，再用`new Map()`把这个数组转换为map对象，使用size属性即可查看对象长度
* 数组去重：使用`new Set()`把数组转换为集合，此时就已经去重了，再用[...]展开运算符把集合转成数组即可
* 使用`typeof`可判断基本类型，判断对象和数组得到'object'，判断函数为'function'
* 判断引用数据类型使用`instanceof`，判断是否是某个构造函数的实例，但是3种引用类型都是Object的实例，此外数组是Array，函数时Function

### 实用方法
* 时间戳转换为标准时间格式
```javascript
function formatDate(){
  function addZero(num){
    if(num < 10){
      return '0'+num
    } else {
      return num
    }
  }
  const date = new Date();
  let years = date.getFullYear(),
  months = date.getMonth() +1,
  days = date.getDate(),
  hours = date.getHours(),
  minutes = date.getMinutes(),
  seconds = date.getSeconds();
  const time = (years + "-" + addZero(months) + "-" + addZero(days) + " " + addZero(hours) + ":" + addZero(minutes) + ":" + addZero(seconds));
  return time;
}
```
* vue中定义函数时接收事件参数外的其他参数
```javascript
// 方法1：
@change="handleChange(arguments,index)"
// arguments为事件默认传递的参数数组，此时需要手动去获取需要的值。index是自己额外想要传递的参数

// 方法2
@change="(status)=>{handleChange(status,index)}"
// status即为事件参数传递的值，index是自己额外传递的值
```