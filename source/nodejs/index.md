---
title: nodejs
date: 2021-09-08 17:00:48
---
### 记录在学习nodejs过程中比较重要的基础和语法
1. 最原始的创建一个简单服务
```javascript
var http = require("http")
var fs = require("fs")
http.createServer((req,res)=>{
  console.log("请求地址:",req.url);
  res.setHeader("Content-Type","text/plain ; charset=UTF-8")
  if(req.url == "/file"){
    fs.readFile("./file.json",(err,data)=>{
      if(err){
        return res.end("404 not found")
      }
      // res.statusCode = 302           //设置重定向
      // res.setHeader("Location","/")
      res.end(data)
    })
  } else if(req.url == "/") {
    res.end("主页")
  } else {
    res.end("404 not found")
  }
}).listen(3000,()=>{
  console.log("serve is running at port 3000...");
})
```
2. 使用express框架
```javascript
// 在node.js中
var express = require('express')
var fs = require("fs")
var app = express()
// app.use(express.static("./public/"))    //开放public文件夹访问权限
app.use("/public",express.static("./public/"))    //开放public文件夹访问权限,推荐使用这种方式，比较好区分路径
// 全局设置跨域和Content-Type
app.all("*",function(req,res,next){
  res.header({"Access-Control-Allow-Origin":"*","Content-Type":"application/json;charset=UTF-8"})
  next()
})

app.get("/",(req,res)=>{
  res.send("首页")
})

// 读取文件并返回数据,不同的请求方式分开处理
app.get("/file",(req,res)=>{
  console.log(req.query)
  fs.readFile("./file.json",(err,data)=>{
    if(err){
      res.send("404 not found")
    } else {
      // res.setHeader的别名是res.header。设置跨域和Content-Type防止中文乱码，全局设置后就不用再设置了
      // res.header({"Access-Control-Allow-Origin":"http://127.0.0.1:5500","Content-Type":"application/json;charset=UTF-8"})
      res.send(data)
    }
  })
})
app.post("/file",(req,res)=>{
  console.log(req.body);
  fs.readFile("./file.json",(err,data)=>{
    if(err){
      res.send("404 not found")
    } else {
      res.send(data)
    }
  })
})

app.listen("3000",()=>{
  console.log("serve is running at port 3000");
})

// 在json文件中
[
  {
    "name":"zs",
    "age":18,
    "country":"中国"
  }
]

// 在HTML中，通过ajax访问接口获取数据
var ajax = new XMLHttpRequest()
ajax.open("get","http://127.0.0.1:3000/file")
ajax.send()
ajax.onreadystatechange = function(){
   if(ajax.readyState == 4 && ajax.status == 200){
     console.log("成功请求数据")
     data = JSON.parse(ajax.responseText)
     console.log(data)
   }
}
```
可以看到，成功通过node创建的服务来访问接口并获取到数据
<div style="display:flex;align-items:center;justify-content:space-between">
   <img src="node1.png"/>
   <img src="node2.png"/>
</div>