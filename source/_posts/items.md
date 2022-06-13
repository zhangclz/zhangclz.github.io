---
title: tips
date: 2021-09-15
tags: 概念
categories: 日常
description : 一些实用的方法，操作和灵感
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

* 读取本地excel文件与导出

**在项目中，读取使用一个插件：xlsx(需要使用0.16.0版本，高版本引入不了)，导出还需要另外一个插件：file-saver**
步骤：
1. 安装：`npm install xlsx@0.16.0 -S`和`npm install file-saver`
2. 在需要使用的页面中引入：`import XLSX from 'xlsx'`、`import fs from 'file-saver'`
3. 导入的函数，这个函数是input输入框type="file"类型的change事件回调
```javascript
importFileDemo(obj) {
   var f = obj.target.files[0];
   //初始化新的⽂件读取对象，浏览器⾃带，不⽤导⼊
   var reader = new FileReader();
   //绑定FileReader对象读取⽂件对象时的触发⽅法
   reader.onload = function (e) {
     //获取⽂件⼆进制数据流
     var data = e.currentTarget.result;
     //利⽤XLSX解析⼆进制⽂件为xlsx对象
     var wb = XLSX.read(data, { type: "binary" });
     //利⽤XLSX把wb第⼀个sheet转换成JSON对象
     //wb.SheetNames[0]是获取Sheets中第⼀个Sheet的名字
     //wb.Sheets[Sheet名]获取第⼀个Sheet的数据
     var j_data = XLSX.utils.sheet_to_json(wb.Sheets[wb.SheetNames[0]]);
   };
   //使⽤reader对象以⼆进制读取⽂件对象f，
   reader.readAsBinaryString(f);
}
```
4. 导出函数，需手动调用
```javascript
outxlsx(json, fields, filename = ".xlsx") {
   //导出xlsx
   json.forEach((item) => {
     for (let i in item) {
       if (fields.hasOwnProperty(i)) {
         item[fields[i]] = item[i];
         }
         delete item[i]; //删除原先的对象属性
     }
   });
   let sheetName = filename; //excel的文件名称
   let wb = XLSX.utils.book_new(); //工作簿对象包含一SheetNames数组，以及一个表对象映射表名称到表对象。XLSX.utils.book_new实用函数创建一个新的工作簿对象。
   let ws = XLSX.utils.json_to_sheet(json, {
     header: Object.values(fields),
   }); //将JS对象数组转换为工作表。
   wb.SheetNames.push(sheetName);
   wb.Sheets[sheetName] = ws;
   const defaultCellStyle = {
     font: { name: "Verdana", sz: 13, color: "FF00FF88" },
     fill: { fgColor: { rgb: "FFFFAA00" } },
   }; //设置表格的样式
   let wopts = {
     bookType: "xlsx",
     bookSST: false,
     type: "binary",
     cellStyles: true,
     defaultCellStyle: defaultCellStyle,
     showGridLines: false,
   }; //写入的样式
   let wbout = XLSX.write(wb, wopts);
   let blob = new Blob([this.s2ab(wbout)], {
     type: "application/octet-stream",
   });
   fs.saveAs(blob, filename + ".xlsx");
}
s2ab(s) {
   var buf;
   if (typeof ArrayBuffer !== "undefined") {
     buf = new ArrayBuffer(s.length);
     var view = new Uint8Array(buf);
     for (var i = 0; i != s.length; ++i) view[i] = s.charCodeAt(i) & 0xff;
     return buf;
   } else {
     buf = new Array(s.length);
     for (var i = 0; i != s.length; ++i) buf[i] = s.charCodeAt(i) & 0xff;
     return buf;
   }
},
```


### 位运算

**1. 与： &**
> 其运算规则是：参与运算的数字，低位对齐，高位不足的补零，如果对应的二进制位同时为 1，那么计算结果才为 1，否则为 0。因此，任何数与 0 进行按位与运算，其结果都为 0

**都为1才是1，其余情况都为0**
实用场景：判断数字奇偶性，奇数和1与，得1。 偶数和1与，得0


**2. 或：|**
> 其运算规则是：参与运算的数字，低位对齐，高位不足的补零。如果对应的二进制位只要有一个为 1，那么结果就为 1；如果对应的二进制位都为 0，结果才为 0

**有1就1，都0才0**


**3. 异或：^**
> 其运算规则是：参与运算的数字，低位对齐，高位不足的补零，如果对应的二进制位相同（同时为 0 或同时为 1）时，结果为 0；如果对应的二进制位不相同，结果则为 1

**相同为0，不同为1**
特性：任何数和自己异或，得0。 任何数和0异或，得本身。 满足交换律和结合律
场景：判断两个数是否相等、数组中只出现一次的数


**4. 取反：~**
> 其运算规则是：只对一个操作数进行运算，将操作数二进制中的 1 改为 0，0 改为 1

**0变1，1变0**