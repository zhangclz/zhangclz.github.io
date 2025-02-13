---
title: tips
date: 2021-09-15
tags: 概念
categories: 日常
description : 一些实用的方法和操作
---
# JavaScript
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

# 实用方法
### 时间戳转换为标准时间格式
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
### 时间格式转换高级版
```javascript
function formatTime(date, fmt) {
  let time = date ? new Date(date) : new Date();
  if (!fmt) {
    fmt = "yyyy-mm-dd";
  }
  let opt = {
    "y+": time.getFullYear().toString(),
    "m+": (time.getMonth() + 1).toString(),
    "d+": time.getDate().toString(),
    "H+": time.getHours().toString(),
    "M+": time.getMinutes().toString(),
    "S+": time.getSeconds().toString(),
  };
  for (const key in opt) {
    if (Object.hasOwnProperty.call(opt, key)) {
      let ret = new RegExp(key).exec(fmt);  //使用正则获取传入的时间格式的对应位的长度
      if (ret) {
        fmt = fmt.replace(
          ret[0],
          ret[0].length == 1  //如果传入的格式对应的长度小于或等于时间值的长度，就直接使用这个时间值，否则用零在前面补齐到时间值的长度
            ? opt[key]
            : opt[key].padStart(ret[0].length, "0")
        );
      }
    }
  }
  console.log(fmt);
  return fmt
}

// 接收两个参数：第一个为时间，各种格式都可以。第二个为时间格式，默认只有年月日。
formatTime("","yyyy-mm-dd HH:MM:SS");
```

### 内存单位换算
```javascript
function formatSizeUnits(kb) {
  let units = ['KB', 'MB', 'GB', 'TB', 'PB'];
  let unitIndex = 0;

  while (kb >= 1024 && unitIndex < units.length - 1) {
      kb /= 1024;
      unitIndex++;
  }

  return `${kb.toFixed(2)} ${units[unitIndex]}`;
}
```

### 根据屏幕宽度和宽高比动态设置根字体大小
主要使用这个语法`document.documentElement.style.fontSize='16px'`，设置 html 的字体大小
配合 window.resize 事件，动态计算字体大小并赋值
```javascript
function setRem () {
  var baseSize = 16 // 基准大小
  var basePc = baseSize / 1920 // 表示1920的设计图,使用100PX的默认值
  var vW = window.innerWidth // 当前窗口的宽度
  var vH = window.innerHeight // 当前窗口的高度
  // 非正常屏幕下的尺寸换算
  var dueH = (vW * 1080) / 1920
  if (vH < dueH) {
    // 当前屏幕高度小于应有的屏幕高度，就需要根据当前屏幕高度重新计算屏幕宽度
    vW = (vH * 1920) / 1080
  }
  var rem = vW * basePc; // 以默认比例值乘以当前窗口宽度,得到该宽度下的相应font-size值
  (window as any).curRem = rem
  document.documentElement.style.fontSize = rem + 'px'
}
// 另一种方式
function setRem1 () {
  const baseSize = 16
  // 当前页面宽度相对于 1920宽的缩放比例，可根据自己需要修改。
  const scale = document.documentElement.clientWidth / 1920
  // 设置页面根节点字体大小（“Math.min(scale, 2)” 指最高放大比例为2，可根据实际业务需求调整）
  document.documentElement.style.fontSize = baseSize * Math.min(scale, 1) + 'px'
}
// 加个防抖，操作之后200毫秒内没有再次操作才执行。（而另一个节流是立马执行，然后在指定时间内无法再次执行）
window.onresize = _.debounce(function () {
  setRem()
},200)
```
再配合 postcss 的插件，`postcss-pxtorem`插件可以将 px 单位转换为 rem。（注意，目前不要使用最新版 6.0.0，有 bug。使用 5.1.1 即可）

### vue中定义函数时接收事件参数外的其他参数
```javascript
// 方法1：
@change="handleChange(arguments,index)"
// arguments为事件默认传递的参数数组，此时需要手动去获取需要的值。index是自己额外想要传递的参数

// 方法2
@change="(status)=>{handleChange(status,index)}"
// status即为事件参数传递的值，index是自己额外传递的值
```

### 读取本地excel文件与导出

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
1. 导出函数，需手动调用
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


# 位运算

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

### 文件下载
1. 直接使用a标签下载，herf设置为下载地址
前提条件：响应头有`content-disposition`属性，浏览器才会触发下载操作。如果没有，则需要手动给a标签添加`download`属性，设置下载的文件名，也可以触发浏览器的下载操作
```javascript
const a = document.createElement('a') // 创建a标签
a.style.display = 'none' // 使之不可见
a.href = 'https://xxx.com' // 下载地址
a.download = '' // 设置文件名
document.body.appendChild(a) // 将a标签插至页面中
a.click() // 触发a标签事件
a.remove() // 直接移除a标签元素，更简洁高效，并不需要先获取父元素再调用removeChild去移除
```
这种方式服务器的文件数据流会直接持续流向用户硬盘，直到下载完成，表现出来就是下载动作会立即响应
2. 使用ajax，fetch等先获取blob二进制数据，然后手动创建一个下载链接，再使用a标签下载，此时是从浏览器的内存中下载，因此没有网络也可以
如果获取到的数据不是blob，则需要先使用`new Blob([xxx])`转为blob，但一般情况下从后端接口获取到的都是blob，然后使用`URL.createObjectURL(blob)`创建一个下载地址，然后操作就和直接使用a标签一样了，只是必须要设置download，因为没有响应头了
```javascript
  const downloadUrl = URL.createObjectURL(res); // 创建下载地址
  //  这种方式可以获取文件名
  const contentDisposition = res.headers['content-disposition'];
  const fileName = decodeURI(contentDisposition.split('filename=')[1]);
  const a = document.createElement("a")
  a.href = downloadUrl
  a.download = fileName; // 设置文件名
  document.body.appendChild(a);
  a.click();
  URL.revokeObjectURL(downloadUrl); // 清除创建的下载地址
  document.body.removeChild(a);

```
这种方式不适合用于大文件下载，因为它会先把文件全部传输到浏览器内存中，再创建下载地址，表现为点击下载后没反应，过了一会才会开始下载

### vite

**打包**
在开发vite+vue3项目时，如果不对vite进行配置，默认是不会进行代码转换的，就会导致打包后，不兼容低版本浏览器。
 ```javascript
// vite.config.js
// 1. 先下载  `@vitejs/plugin-legacy`插件
// 2. 进行配置
defineConfig({
  build: {
    target: "es2015" // 按理说只添加这个就行了，不确定，后续可验证一下
  },
  // 稳妥起见加上这个插件
  plugins: [
    legacy({
      targets: [
        "> 1%",
        "not ie 10",
        "not op_mini all",
        "chrome >= 78",
        "edge >= 78",
        "firefox >= 72",
        "safari >= 13",
        "opera >= 66"
      ]
    })
  ]
})
```
这样配置之后，打包的时候就会进行代码转换，以兼容目标浏览器