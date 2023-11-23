---
title: ajax
date: 2021-09-10
tags: ajax
categories: JS
description: ajax的基本使用以及使用promise对ajax进行简易封装
---
### 原生ajax的使用
```javascript
// html 先模拟一个点击事件
<button class="btn">点击发送请求</button>
// js
var btn = document.querySelector(".btn")
btn.onclick = function(){
  var xhr = new XMLHttpRequest()
  xhr.open("get","http://127.0.0.1:3000/file")
  xhr.send()
  xhr.onreadystatechange = function(){
    if(xhr.readyState == 4 && xhr.status == 200){
      console.log(xhr.responseText);
    }
  }
}
// 在后端配置正确的情况下就可以发送请求到指定url并获取数据了
```
### 使用promise进行封装
当需要发送大量请求的时候，上面的原生用法显然不合适，每个请求都要写很多代码，所以就需要对请求进行封装，简化代码，也方便使用
点击事件同上
```javascript
var request =  function(params){
  // 给必要参数赋默认值
  let method = params.method || "get"
  let data = params.data || {}
  let url = params.url || "/"
  let baseUrl = "http://127.0.0.1:3000"

  return new Promise((resolve,reject)=>{
    let xhr = new XMLHttpRequest()
    // 区分是get还是post请求，对参数进行不同的处理
    if(method == "get"){
      // 把对象转换为get参数格式
      let param = JSON.stringify(data).replace(/:/g,'=').replace(/,/g,'&').replace(/{/g,'?').replace(/}/g,'').replace(/"/g,"")
      xhr.open("get",baseUrl+url+param)
      xhr.send()
    } else {
      xhr.open("post",baseUrl+url)
      xhr.send(JSON.stringify(data))
    }
    xhr.onreadystatechange = function(){
      if(xhr.readyState == 4){
        if(xhr.status == 200){
          resolve(xhr.responseText)
        } else {
          reject(xhr.responseText)
        }
      }
    }
  })
}
// 调用封装好的函数
btn.onclick = function(){
  request({
    method: "post",
    url: "/file",
    data: {
      name: "zs"
    }
  }).then(res=>{
    console.log(res)
  }).catch(err=>{
    console.log(err)
  })
}
// 以上就完成了ajax最简单的封装和使用
```
---
后端配置请点击[这里](https://zhangclz.github.io/nodejs/)进行查看，第二条express

### axios的通用封装
1. 配置axios实例
```javascript
// instance.js
import axios from 'axios'

let instance = new axios.create({
  headers: {
    'Content-Type': 'application/json',
  },
  baseURL: 'http://xxx.com',
  timeout: 60000
})
// 适配器，用于使用其他框架的请求方法比如wx.request然后将数据返回。或者做数据mock，模拟服务，返回mock数据。很少使用
// instance.defaults.adapter = function (config) {
//   return new Promise((resolve,reject)=>{
//     resolve(111) //将不再发起请求，直接返回111
//   })
// }

// 请求拦截器，一般用于在请求头里面添加数据
instance.interceptors.request.use((config)=>{
  return config
})

// 响应拦截器，用于在接收到后端请求后对错误进行统一处理和返回简化后的结果，有两个回调函数，一个成功一个失败的
instance.interceptors.response.use((response)=>{
  if(response.status == 200 && response.data.status == 200){
    return response.data
    // return Promise.resolve(response.data) 这种写法也可以
  } else if(response.status == 200 && response.data.status == 500){
    return Promise.reject(response.data.error)
  }
},(error=>{
  console.log('error',error);
}))

export { instance }
```

2. 配置全局请求方法
```javascript
//service.js或api.js
import { instance } from "./instance";

// axios会自动把get请求传递的对象参数转换为标准的get参数格式，因此不需要手动转换，如果直接用/传递，则任需手动转换
// export const test = (params) => {
//   return instance.get(`/api/test`, {params})
// }
export const test = (phone,code) => {
  return instance.get(`/api/test/${phone}/${code}/true`)
}

export const login = (params) => {
  return instance.post(`/api/test`, params);
};
```

### axios封装请求队列和请求取消
默认情况下，axios的各个请求是异步的，意味着同一时间，可以发起多个请求，接口返回的顺序和后端处理速度和网络传输速度有关，大部分情况下这没有什么问题。
然而在一些场景，比如网络环境差，或者后端处理时间长的情况下，如果前端没有做函数防抖、节流或者加载中的等待，那么就可能会造成发起重复请求，并且这些重复请求传递的参数可能会不一致，又因为返回的顺序不可控，所以会导致页面显示和预想的不符。
针对这种情况，除了在触发事件时做限制，当然这是常规做法，也可以从发起请求时做处理，就是使用请求队列。

思路：将每个请求先放到一个请求队列中，此时如果执行队列中没有正在处理的请求，就从请求队列中读取一个请求到执行队列中执行，当这个请求完成后，清空执行队列，然后从请求队列读取一个请求，以此往复。
```javascript
// request.js完整代码
import axios from "axios"

// 通用配置
const instance = axios.create({
  baseURL: "http://xxx.com",
  timeout: 6000
})
// 请求拦截器，主要用于向请求头中添加token或其他属性
instance.interceptors.request.use(config => {
  return config
}, err => {
  return Promise.reject(err)
})
// 响应拦截器，针对不同后端风格的数据结构做响应处理
instance.interceptors.response.use(response => {
  // ...其他判断逻辑
  return response.data
}, err => {
  return Promise.reject(err)
})

// 请求队列
let requestQueue = []
// 执行队列
let executeQueue = []
// 请求队列函数，将请求添加到队列中
function requestQueueFn(config,source) {
  return new Promise((resolve, reject) => {
    if(executeQueue.length != 0 && config.cancel){
      // 重复请求并且已经在执行，则取消这个请求
      if(executeQueue[0].config.url === config.url){
        executeQueue[0].source.cancel('cancel')
      }
    }
    // 请求重复但是还没有开始执行，删除之前的请求，并将最新的一个请求添加到请求队列末尾
    let index = requestQueue.findIndex(item => item.config.url === config.url)
    if(index > -1){
      requestQueue.splice(index,1)
    }
    requestQueue.push({
      config,
      resolve,
      reject,
      source,
    })
    // 执行队列为空，读取一个请求执行
    if(executeQueue.length === 0){
      executeQueueFn(requestQueue[0])
      requestQueue.shift()
    }
  })
}
// 执行一个请求
function executeQueueFn(configObj) {
  executeQueue.push(configObj)
  // 真正发起请求，请求完成后继续读取并执行
  instance(configObj.config).then(res => {
    configObj.resolve(res)
    executeQueue.shift()
    if (requestQueue.length > 0) {
      executeQueueFn(requestQueue[0])
      requestQueue.shift()
    }
  }).catch(err => {
    executeQueue.shift()
    configObj.reject(err)
    if (requestQueue.length > 0) {
      executeQueueFn(requestQueue[0])
      requestQueue.shift()
    }
  })
}
// 对外暴露的请求函数
export const request = (config) => {
  if(!config.method || !config.url){
    return Promise.reject("缺少必要参数")
  }
  // 如果不想使用队列和取消请求，就不添加cancel属性，此时就和常规的axios请求一样
  if(!config.cancel){
    instance(config)
  } else {
    let source = axios.CancelToken.source(); 
    // 带上cancelToken的config
    let cf = {
      ...config,
      cancelToken: source.token,
    }
    return requestQueueFn(cf,source)
  }
}
```
使用请求队列后，方便的一点就是如果想要针对某个操作做防抖，那么直接在发起请求时，额外传递一个cancel属性即可