---
title: 数组的方法
date: 2021-12-13
tags: 语法
categories: JS
description: javascript中数组的原生方法以及用法
---
### Array.prototype.map()
> map() 方法创建一个新数组，其结果是该数组中的每个元素是调用一次提供的函数后的返回值
> 而filter的返回值是提供的函数返回值为true的这次循环的元素数组
```javascript
let arr = [1,2,3,4,5,6]
arr.map((v)=>{
  v = v+1
})
console.log(arr)
// 同样输出[1,2,3,4,5,6]
// map方法对v直接赋值不会改变原数组，如果v是个引用数据类型，则改变这个数据的值会引起原数组的改变
let objarr = [{a:1},{a:2},{a:3},{a:4}]
objarr.map((v)=>{
    v.a = 6
})
console.log(objarr)
// 数组中每个对象的a都变为了6
```
数组map方法性能不如for和foreach，主要使用场景是遍历数组并对每个元素进行处理并返回一个全新的数组的情况，如果只是遍历取值不推荐使用map
```javascript
let arr = [1,2,3,4,5,6]
let newarr =  arr.map((v)=>{
  return ++v
})
console.log(newarr)
// 输出[2,3,4,5,6,7] 
```

**forEach、for-in与for-of**
1. foreach 方法没办法使用 break 语句跳出循环，或者使用return从函数体内返回
2. for-of 可以使用 break, continue 和 return,也可以用来遍历字符串
3. for-in常用于循环对象，不用于循环数组