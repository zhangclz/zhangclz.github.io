---
title: 前端模块化
# date: 2021-09-02 15:17:10
date: 2021-09-02
description: 讲述了前端模块化的概念、发展历程以及容易让人混淆的export、export default、module.exports和exports的区别和使用方法
tags:
  - 概念
  - 语法
categories: JS
---
![bg](https://pica.zhimg.com/v2-c205ca9e942ce9ce6c53a39bdce20a04_1440w.jpg?source=172ae18b)
### 为什么前端需要模块化
以前没有模块化的时候，js代码都是直接写在html文件的script标签中，但随着业务的复杂，特别是在Ajax诞生以后，前端能做的事情越来越多，代码量飞速增长，开发者们开始把js代码写到独立的js文件中，与html文件解耦。
再后来，项目越来越大，一个项目需要很多个开发者合作，导致更多的js文件被引入进来。这就出现了一个问题：不同的js文件中会出现变量冲突，全局变量污染开始成为开发者的噩梦。
### 模块化原型
为了解决这些问题，开发者用命名空间的方法来解决命名冲突
```javascript
//a.js
app.a = {}
app.a.name = "a"

//b.js
app.b = {}
b.name = "b"
```
但是这样开发者还是可以随意的获取和修改其他人js文件中的数据，这显然是不好的

于是开发者利用js语言函数作用域，闭包特性来解决这个问题
```javascript
//a.js
app.Afn = (function(){
  var name = "zs"
  var getName = function(){
    return name
  }
})()

//b.js
app.Bfn = (function(){
  var name = "ls"
  var getName = function(){
    return name
  }
})()
```
这样就可以使用getName方法来获取不同js文件中数据，但是不能对其进行修改，若想修改则必须在原js文件中添加修改数据的函数。
```javascript
//接着上面的a.js代码
var setName = function(val) {
  name = val
}
console.log(getName())    //zs
setName("new name")
console.log(getName())    //new name
```
这样虽然保证了数据的安全，也对外提供了访问的接口，但是这样任然存在一定的问题，比如模块加载有先后顺序，所以在访问其他模块的时候有可能会失败。其次，当一个前端应用业务规模足够大后，这种依赖关系又变得异常难以维护。
### CommonJS
既然JavaScript需要模块化来解决上面的问题，那就需要制定模块化的规范，CommonJS就是解决上面问题的模块化规范，它规定：**Node.js 应用由模块组成，每个文件就是一个模块，有自己的作用域。在一个文件里面定义的变量、函数、类，都是私有的，对其他文件不可见。**每个模块都有两个属性：module和require

**module**：代表当前模块，是一个对象。exports是module对象上的一个属性，保存了当前模块要导出的接口或者变量
**require**：加载某个模块，获取那个模块使用exports导出的值，并且这个值是那个模块导出对象的拷贝，因此修改原模块中的数据不会对已经导出的值产生任何影响
```javascript
//a.js
var name = "zs"
var age = 18
var fn = function(){
  console.log(name)
}
module.exports.name = name
//exports.name的name只是一个变量名，可以任意取名，比如module.exports.Aname = name也是可以的

//b.js
var b = require(./a.js)
// 此时b就是a中导出的模块，相当于是a中module对象的exports属性
console.log(b.name)  //zs
```
导出时可以使用简写，直接`exports.name = name` 这和`module.exports.name = name`是同样的效果。因为模块中exports是一个私有变量，它指向了module.exports
对exports直接赋值是无效的，比如`exports = name`，这只是让exports不再指向module.exports

当导出的数据是一个单一的值，`exports.name = name`即可，若要导出多个值，则使用`module.exports = { }`
当然，不能使用`exports = { }`
```javascript
//a.js
var name = "zs"
var age = 18
var fn = function(){
  console.log(name)
}
module.exports = {
  name:name,
  age:age,
  fn:fn
}
// 变量名和属性名相同可简写
// module.exports = {
//   name,
//   age,
//   fn
// }

// 当然变量名可以自定义，比如
// module.exports = {
//   Aname:name,
//   Aage:age,
//   Afn:fn
// }

//b.js
var b = require(./a.js)
console.log(b.name)  //zs
console.log(b.age)  //18
b.fn()  //zs
```
### ES6模块
CommonJS是服务于服务端的，在代码运行后才能确定导出的内容。从ES6开始，在语言标准的层面上，实现了模块化功能，而且实现得相当简单，完全可以取代 CommonJS规范，成为浏览器和服务器通用的模块解决方案。
ES6 Module模块功能主要由两个命令构成：**export** 和 **import**
**export** 命令用于导出模块的对外接口，**import** 命令用于导入其他模块导出的内容
```javascript
// a.js
export let name = "zs"
export let age = 18
export let fn = function(){
  console.log(name)
}
// 如果有多个的单个导出可以写成对象导出，更简洁，上面代码等价于
let name = "zs"
let age = 18
let fn = function(){
  console.log(name)
}
export {
  name,age,fn
}

// b.js
import { name,age,fn } from "./a.js"
console.log(name)  //zs
fn()  //zs
```
相信已经有聪明的孩子看出来有何不妥之处了，在导入的时候需要知道导出的变量叫啥，而且导出多少个变量，导入时若想全部导入就得全部写在对象中，有些时候这就很麻烦。所以ES6 Module 规定了一种方便的用法：`export default` 指定默认输出，一个文件只能有一个默认输出。
```javascript
// 接着上面的代码
// 此时a.js导出可写为
export default {
  name,age,fn
}
// emmm感觉没啥变化，就加了个default

// 不过b.js中导入变化较大
import a from './a.js'    //a只是自定义的对象名称，可任意修改
console.log(a.name)   //zs
a.fn()  //zs
```
如果不用export defaul这种导出，也可以使用`import * as xxx from xxxxxx`这个语法来将导入的数据加载到指定对象上
```javascript
// a.js导出
export {
  name,age,fn
}
// b.js导入
import * as a from './b.js'   //将导入的所有值加载到a这个对象上
console.log(a.name)   //zs
a.fn()  //zs
// 这个就和默认导出再正常导入很相似
```
### ES6 Module 和 CommonJS 的区别
CommonJS 只能在运行时确定导出的接口，实际导出的就是一个对象。而ES6 Module的设计思想是尽量的静态化，使得编译时就能确定模块的依赖关系，以及导入和导出的变量，也就是所谓的"编译时加载"。
因此，import 命令具有提升效果，会提升到整个模块的头部，首先执行。

另外，之前提到require的是被导出的值的拷贝。也就是说，一旦导出一个值，模块内部的变化就影响不到这个值。而ES6 Module中，改变原模块中变量的值，已经导出的值也会跟着改变。

---
**参考资料**
[前端科普系列-CommonJS：不是前端却革命了前端](https://zhuanlan.zhihu.com/p/113009496)