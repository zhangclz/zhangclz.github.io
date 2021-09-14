---
title: 对象的方法
date: 2021-09-14
tags: 语法
categories: JS
description: 本文简要介绍javascript中对象的原生方法以及用法，持续更新...
---
### Object.assign
引用MND的话来说:
> Object.assign() 方法用于将所有可枚举属性的值从一个或多个源对象分配到目标对象。它将返回目标对象。

用我的话来说就是：将一个对象的可枚举属性转移到另一个对象中,这个方法的返回值是转移后的新对象。
**语法：Object.assign(target,source)**
这种转移有以下特性：
* 目标对象（新对象）中的属性如果和源对象（旧对象）中属性相同，则会被覆盖
```javascript
let oldObj = {
  name:'zs'
}
let newObj = {
  name:'ls'
}
Object.assign(newObj,oldObj)
console.log(newObj.name)   //zs
``` 
* 如果目标对象是空的，则可以理解成拷贝，拷贝的是属性值。如果属性值是简单数据类型，则为是深拷贝。
```javascript
let oldObj = {
  name:'zs'
}
let newObj = {}
Object.assign(newObj,oldObj)
console.log(newObj.name)    //zs
oldObj.name = 'ls'
console.log(oldObj.name)    //ls
console.log(newObj.name)    //zs
// 由此可见转移过后，改变原对象中属性值不会影响到新对象，同样新对象属性改变也不会影响原对象
```
* 如果属性值是复杂数据类型，则为浅拷贝。
```javascript
let oldObj = {
  name:'zs',
  depObj: {
    age:18
  }
}
let newObj = {}
Object.assign(newObj,oldObj)
console.log(newObj.depObj.age)    //18
oldObj.depObj.age = 88
console.log(oldObj.depObj.age)    //88
console.log(newObj.depObj.age)    //88
// 由此可见，改变原对象中的复杂数据类型属性会影响到新对象，同样对新对象中复杂数据类型属性进行操作也会影响到原对象
```

既然这个方法可以实现对象的拷贝，就简单说下其他的对象拷贝方法：
1. JSON.stringify和JSON.parse
这两个方法分别是将js对象转换成json字符串和将json字符串解析成js对象，先用`JSON.stringify()`再用`JSON.parse()`可达到和`Object.assign()`一样的拷贝效果，同样的如果属性是复杂数据类型只能拷贝值的应用，属于浅拷贝
```javascript
let obj = {
  name: 'zs'
}
let newObj = JSON.parse(JSON.stringify(obj))
console.log(newObj.name)    //zs
obj.name = 'ls'
console.log(obj.name)    //ls
console.log(newObj.name)    //zs
// 和Object.assign()效果一样，复杂数据类型效果也一样
```
2. 使用递归实现深拷贝
对于属性值是复杂数据类型的对象或者数组，都可以使用递归来进行深拷贝，不管数据嵌套多少层都可以
```javascript
 let deepClone = function(old){
 if(old) {
   if(typeof(old) == 'object') {                  //如果拷贝的数据是复杂数据类型
     let newObj = Array.isArray(old) ? [] : {}    //判断是数组还是对象
     for (const key in old) {
       if (old.hasOwnProperty.call(old, key)) {   //不拷贝原型上的属性
         if(typeof(old[key]) == 'object'){        //如果对象或数组的属性是复杂数据类型，则用递归再对属性执行一次函数
           newObj[key] = deepClone(old[key])
         } else {
           newObj[key] = old[key]                 //属性是基本数据类型直接赋值
         }
       }
     }
     return newObj
   } else {                                       //如果拷贝数据是基本数据类型，直接赋值返回
     let newObj = undefined
     return newObj = old
   }
 }
}
// 使用：
let ob = {
  name: 'zs',
  dep:{
    age:18
  }
}
let newOb = deepClone(ob)
console.log(newOb.dep.age)  //18
ob.dep.age = 88
console.log(ob.dep.age)  //88
console.log(newOb.dep.age)  //18
// 由结果可知，成功进行了深拷贝，拷贝后源数据和新数据不会相互影响
```
---