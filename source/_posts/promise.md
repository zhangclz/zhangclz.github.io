---
title: promise
date: 2021-12-13
tags: 语法
categories: JS
description: ES6中Promise的用法以及其语法糖async await的用法
---
### Promise
> Promise对象用于表示一个异步操作的最终完成 (或失败)及其结果值

Promise是一个对象，我们使用它时需要通过`new`关键字将其实例化，在其内部执行异步操作。它接收两个参数：resolve和reject，用来接收Promise执行成功和失败的结果
```javascript
let fn = function(){
  return new Promise((resolve,reject)=>{
    setTimeout(()=>{
      console.log(123)
      resolve(666)
    },2000)
  })
}
fn().then(res=>console.log(res)).finally(()=>{console.log(555)})
// 函数执行两秒钟后输出123和666和555
```
通过以上代码，就可以简单的实现在fn函数执行完成以后再执行其他的函数，而fn函数中是异步操作。将下一个函数写在then或者catch回调里即可。finally回调是then或者catch执行完成后都会执行的回调

promise还有几个常用的方法：
* Promise.all(iterable)  这个方法返回一个新的promise对象，该promise对象在iterable参数对象里所有的promise对象都成功的时候才会触发成功
* Promise.any(iterable)  接收一个Promise对象的集合，当其中的一个 promise 成功，就返回那个成功的promise的值
* Promise.reject(reason)  返回一个状态为失败的Promise对象，并将给定的失败信息传递给对应的处理方法
* Promise.resolve(value)  返回一个状态为fulfilled的Promise对象

### async await
> async和await关键字让我们可以用一种更简洁的方式写出基于Promise的异步行为，而无需刻意地链式调用promise，可以理解为promise的语法糖
```javascript
async function f1() {
  setTimeout(()=>{
    console.log(1)
  },2000)
}
f1().then(()=>{console.log(2)})
// 先输出2，两秒钟后输出1
// 用async修饰函数，可以在函数执行完成后调用then和catch回调，但是如果里面不使用promise，则代码执行顺序不会受任何影响

改造一下：
async function f2() {
  return new Promise((res,rej)=>{
    setTimeout(()=>{
      console.log(1)
      res(2)
    },2000)
  })
}
f2().then((res)=>{console.log(res)})
// 两秒钟后输出1和2
// 不过这种用法和只使用promise没区别，但是async结合await就不同了

使用async和await再改造：
async function f3() {
  await new Promise((res,rej)=>{
    setTimeout(()=>{
      console.log(1)
      res()
    },2000)
  })
  console.log(2)
}
f3()
// 两秒之后输出1和2，这和上面的结果一样，但是没有使用回调
// 在async修饰的函数内部，await能够阻塞代码，直到接收到promise resolve的结果，这样就可以不使用回调达到将异步操作以代码的顺序同步执行，更加直观
```
ps：注意一点，当对一个使用了promise的函数同时使用了await和then回调，则resolve出来的结果不会被await接收，会被then回调接收
`let aaa = await B().then(res=>console.log(res))` 输出aaa值为undefined，res的值为resolve出来的值