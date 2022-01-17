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