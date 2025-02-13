---
title: ts
date: 2021-09-15
tags: 语法
categories: TS
description: TypeScript的基本语法和用法，以及在vue项目中的各种使用
---
### 变量声明
申明数组：
* `let arr: number[] = []`或者`let arr: Array<number> = []` 申明一个只能放数字的数组，数组长度不受限制。这两种方式类似与js中的字面量和使用new关键字创建
* `let arr: any[] = []`或者`let arr: Array<any> = []` 申明一个常规数组，数组里面可以放所有的数据类型，和js中的数组无差别

申明元组：
* `let tup: [number] = [1]` 申明一个元组，里面必须有一个并且是数字。(元组长度是固定的)

### 类型断言
使用any或者联合类型申明的变量在使用时如果明确知道当前它究竟是什么数据类型，则可以使用类型断言将它指定为确定的数据类型
```typescript
let a: number | string = 'zs'
// 断言方式1
let b: string = (<string>a)   //使用()和<>
// 断言方式2
let b: string = (a as string)   //使用()和as
```
! 和 ? 的使用
* 在TS中 ! 表示变量不是null或者undefined的断言，让它可以通过编译。用在赋值的内容后时，使null和undefined类型可以赋值给其他类型并通过编译，表示该变量值可空
常用在子组件接收父组件传值的地方`@Prop(Number) prop1!: number`
* ? 表示函数参数是非必传。也常用于防御式编程，在访问对象的属性时，在对象后面写 ? 表示对象存在才会去读取属性 `userinfo?.name`
```typescript
function fn(name: string, age?: number){
  console.log(name)   //'zs'
  console.log(age)    //undefined   如果没传这个参数，默认值为undefined，可以手动指定默认值：age: number = 18，此时已经赋值就不能用?了
}
fn('zs')
```
### 枚举类型
枚举类型用于将属性和数字对应，可以更方便的去做逻辑处理。默认值从0开始
```typescript
enum Color {red, blue, green}
console.log(Color.red)   //0

enum Color1 {red = 10, blue = 20, green = 30}
console.log(Color1.red)   //10
```
### interface接口
在TS中接口用来定义一个对象中数据的类型，用interface关键字来定义
```typescript
interface Point {
  x: number,
  y: number
}
// 在申明对象的时候指定对象的接口就能限制属性的数据类型，当对象属性缺失或者数据类型不匹配会报错
let obj: Point = {
  x:1,
  y:2
}
```
申明class类的时候也可以用interface来限制类中的属性和函数，使用implement关键字 `class point implement Point {}`
### 泛型
在定义、函数、接口、类时，不预先指定具体的类型，而是在使用的时候再指定类型限制的一种特性
```typescript
let fn =  <T>(num: T) => {
  return num
}
console.log(typeof(fn(1)))    //number
console.log(typeof(fn("1")))    //string
```

# 梳理
**ts相比于js的优势：**
1. 具有语法错误提示
2. 具有快捷的语法编写提示
3. 变量类型和接口的声明增强了代码可读性

**在ts中变量一旦确定类型，该变量就具有该类型的原生方法，所以不能更改为其他类型**

类型注解：手动指定变量的类型

**声明函数的两种相似方法**
1. `const num: (num: number) => number = num => num`
2. `const num = (num: number): number => num`

**interface（接口）和type（类型别名）的区别**
1. 两者都可以定义一个类型，但interface是通过对象的形式来定义，而type既可以用对象方式，也可以直接指定某一种类型
2. 接口具有合并效果，同名会合并，type则会报错
3. type可使用&符进行类型成员合并
4. 接口可以继承一个或多个接口或类，type无继承
ts文件编译成js文件后，接口会被剔除，他只是在ts文件中起校验和语法提示的作用，并不会被转换成js代码
```typescript
interface  Point {
  x:number;
  y:number;
}
// 等同于
type Point = {
  x:number;
  y:number;
}
// type额外语法
type Point = number
```

**private protected public**
1. public修饰的属性和方法允许在类的外部调用
2. private只允许在该类内部调用，new出来的对象无法调用
3. protected允许在类内和子类内调用，但无法在类外使用

对于private的属性可以通过`get`和`set`方法来间接的进行读取和修改
```typescript
class Person {
  constructor (private _name:string){
    get name(){
      return _name
    }
    set name(name: string){
      this._name = name
    }
  }
}
const person = new Person('zs)
console.log(person.name)  //'zs'  这里会调用get方法去获取类里面的_name
person.name = 'ls'        //先调用set方法去改变_name的值
console.log(person.name)  //'ls'  再调用get方法去获取类里面的_name
```
**static**
> 在类中使用static修饰属性或方法表示这个是直接挂在类上面的，不用实例化也可以调用
但需要注意以下几点
1. static方法只能调用static属性或方法(使用`类名.`或者`this`调用)
2. 非static方法可以在类中通过`类名.`的语法去调用static的属性或方法，而实例化后无法通过对象变量的点语法去访问静态属性或方法

**实例化类的时候会自动执行类的构造器（constructor），并且构造器能接收传进来的参数，在形参前面用修饰符，代表在类里面赋值（语法糖）**
```typescript
class Person {
  // public name:string;
  constructor (public name:string){
  // constructor (name:string){
    // this.name = name
  }
}
const person = new Person('zs')
```
**static修饰符把属性和方法直接挂在类上面，而不是类的实例上，所以不需要实例化就可以调用类里面的属性和方法，而public需要实例化之后才能去调用**
**单例模式：类只能被实例化一次，若重复实例化，则直接返回第一次实例化出来的对象。私有构造器，用静态方法去创建实例**

**.d.ts类型定义文件的作用是将js代码翻译成ts代码来给我们在开发中使用**

**类的装饰器本质上是一个函数，一般情况下通过@符号来使用**
```typescript
// 简单写法
function decoretor(constructor:any){
  constructor.prototype.getName = () => {
    return this.name
  }
}

@decoretor
class Test{
  name:string
  constructor(name:sting){
    this.name = name
  }
}

const instance = new Test('zs')
(instance as any).getName()    // 输出'zs'

// 完整写法，有语法提示
function decoretor(){
  return function <T extends new (...agrs: any[]) => any>(constructor: T){
     return class extends constructor{
       getName(){
         return this.name
       }
     }
   }
}
// 当类初始化的时候就会调用这个装饰器（并不是实例化）
const Test = decorator()(
  class {
    name:string
    constructor(name:string){
      this.name = name
    }
  }
)

// 实例化后可以调用getName方法
const instans = new Test('zs')
instans.getName()   // 输出'zs'
```

**方法的构造器**
```typescript
function fnDecorator(target:any,key:string,descrpitor:PropertyDescriptor){
  // key是被修饰的方法。descrpitor是方法的基本属性，类似于js的object中属性的基本属性，可读可写之类的

  // 普通方法，target对应的是类的prototype
  // 静态方法，对应的是类的构造函数
  console.log(target)
}
class Test {
  @fnDecorator
  getName(){}
}
```

**函数重载**
重载签名：可以有多个，只有函数名和参数，没有函数体
实现签名：只有一个，有函数体

> 只能调用重载签名，根据传递的参数来判断进入的是哪个重载签名

因为函数重载能根据不同的参数类型指定不同的函数返回值，所以函数处理过后的数据能够有语法提示。而直接使用函数只能指定一种返回值类型，大多数情况下不满足需求，就只能返回any类型或联合类型，所以就没有语法提示了
```typescript
type baseType = "image" | "audio" | string;
type msgType = {
  id: number;
  type: baseType;
  msg: string;
};

const msgInfo: msgType[] = [
  {
    id: 1,
    type: "image",
    msg: "第一张图片",
  },
  {
    id: 2,
    type: "audio",
    msg: "一段音频",
  },
  {
    id: 3,
    type: "image",
    msg: "第二张图片",
  },
];

function getMsg(id: number): msgType; //重载签名
function getMsg(type: baseType): msgType[]; //重载签名
function getMsg(params: any): msgType | msgType[] | undefined {   //实现签名
  if (typeof params === "number") {
    for (const item of msgInfo) {
      if(item.id === params){
        return item
      }
    }
  } else {
    return msgInfo.filter((item) => item.type === params);
  }
}

let result = getMsg(2)  //传字符串就会把第二个重载签名和实现签名的函数体结合成一个函数来执行
```

## JS继承
1. **原型链继承**：子构造函数的原型对象属性指向父构造函数的实例化对象
```javascript
// 父构造函数
function Parent () {
  this.name = 'zs'
}
// 子构造函数
function Son () {
  this.age = 18
}
// 父构造函数原型上添加方法
Parent.prototype.fn = function(){
  return this.name
}
// 子继承父
Son.prototype = new Parent()
// 让constructor重新指向之子构造函数，因为每一个构造函数的constructor应该都是指向自己的
Son.prototype.constructor = Son
let son = new Son()
console.log(son.fn());    // 输出zs，子构造函数的实例对象可以使用父构造函数中的属性和方法了
```
不足：子构造函数不能向父构造函数传递参数

2. **对象冒充**：使用call和apply将子构造函数this和其他的参数传递给父构造函数
```javascript
// 父构造函数
function Parent () {
  this.name = 'zs'
}
// 子构造函数
function Son (num) {
  this.age = 18
  // 对象冒充，call传参时this后面的参数是一个一个分开的，apply参数是一个数组，这是唯一区别
  Parent.call(this,num)
}
let son = new Parent(66)
// 此时son对象上会多出name属性
```
不足：此方法只能让子构造函数继承到父构造函数体中的属性和方法，无法继承原型

3. **组合继承**：结合原型链继承和对象冒充
```javascript
// 父构造函数
function Parent (...arguments) {  //这里要使用...arguments才能完整获取apply传递的参数数组，否则只会获取到数组中的第一个参数
  this.name = 'zs'
}
// 子构造函数
function Son (num) {
  this.age = 18
  Parent.apply(this,[num,99])
}

Parent.prototype.fn = function(){
  return this.name
}
// 子继承父
Son.prototype = new Parent()
Son.prototype.constructor = Son

let son = new Son(66)
console.log(son.fn());    // 输出zs，子构造函数的实例对象可以使用父构造函数中的属性和方法了
```
不足：调用了两次父构造函数，浪费内存，降低了代码运行效率
ps：这里不直接让子构造函数的原型等于父构造函数的原型是因为这样写的话子构造函数的实例对象原型链的上一级就不是父构造函数的原型了

4. **寄生组合继承**：使用一个额外的构造函数去获取父构造函数的原型（最佳继承方式）
```javascript
// 父构造函数
function Parent () {
  this.name = 'zs'
}
// 子构造函数
function Son () {
  this.age = 18
  // 继承父构造函数体中的属性和方法   继承第一部分
  Parent.apply(this)
}

Parent.prototype.fn = function(){
  return this.name
}

// 原型继承函数
function _extends (parent,son){
  // 寄生构造函数
  function Middle(){
    this.constructor = Son    //让子构造函数的constructor指向子构造函数自己
  }
  // 将父构造函数的原型赋值给寄生构造函数
  Middle.prototype = parent.prototype
  return new Middle()  //返回的Middle对象实例的原型是指向父构造函数的
  // 因为Middle构造函数体中没有任何属性和方法，它的原型又等于父构造函数的原型，所以new Middle()就相当于只实例化了父构造函数的原型
}

// 给子构造函数的原型赋值，以原型继承函数为中介，间接去获取父构造函数的原型    继承第二部分
Son.prototype = _extends(Parent,Son)

let son = new Son()
console.log(son.name)      //输出zs
console.log(son.fn());    // 输出zs



// 也可以使用Object.create来获取父构造函数的原型
// 但这种方法的复用性和灵活程度不如上面的方法
function _extends (parent){
  // Object.create的返回值是一个对象，并且该对象的原型是传递的参数
  return Object.create(parent.prototype)
}
Son.prototype = _extends(Parent)
// 由于不能在_extends函数中修改constructor的指向，所以只能放到外面
Son.prototype.constructor = Son
let son = new Son()
```

## TS继承
> TS继承使用extends关键字，实现原理和JS的寄生组合继承一致

**super的用法**
1. 用在子构造函数的constructor中，接收的参数是父构造函数的属性，表示在子构造函数中使用父构造函数定义的属性
2. 在子构造函数的constructor外部使用`super.`的语法，可以直接调用父构造函数中的方法（原理是调用父构造函数原型上的方法）

**方法重写(override)**
必须满足3个条件：
1. 和父类方法名相同
2. 参数和父类方法相同
3. 父类方法访问范围必须小于子类重写方法的访问范围（public > protected > private），而且父方法不能是private

## 断言
类型断言使用`as`，类型转换使用`<>`，效果是完全一样的
**类断言**
1. 存在继承关系的父子类可以相互断言，但一般都是父类断言为子类（其实还是因为属性和方法能够重叠）
2. 无关系的类：两个类中所有公有属性或方法存在重叠（完全相同或者一个是另一个子集）

## 抽象类
> 一个在任何地方都不能被实例化的类，使用`abstract`关键字进行创建

* 可以包含0或多个抽象方法。没有方法体，只有方法名，带有`abstract`关键字

好处：
1. 抽象类通常用来充当父类，并且存在抽象方法时，会在子类中强制实现，防止在不同子类中同一个功能的函数名字却不同
2. 防止实例化一个无任何意义的对象

## 泛型
