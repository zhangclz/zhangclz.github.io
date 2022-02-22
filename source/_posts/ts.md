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

# 系统学习
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
两者都可以定义一个类型，但interface是通过对象的形式来定义，而type既可以用对象方式，也可以直接指定某一种类型
ts文件编译成js文件后，接口会被剔除，他只是在ts文件中起校验和语法提示的作用，并不会被转换成js代码
```javascript
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
```javascript
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
**实例化类的时候会自动执行类的构造器（constructor），并且构造器能接收传进来的参数，在形参前面用修饰符，代表在类里面赋值（语法糖）**
```javascript
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
