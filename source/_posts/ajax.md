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