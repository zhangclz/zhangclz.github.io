---
title: ajax
date: 2021-09-10
tags: ajax
categories: JS
description: ajax的基本使用以及使用promise对ajax进行建议封装
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