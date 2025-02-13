---
title: 正则表达式
date: 2021-10-18
tags: 日常
categories: 命令
description: 在js中正则表达式的使用，各种语法和方法
---
### 基本知识点
**元字符**

|字符|含义|
|:-:|:-|
|.|匹配除换行符以外的任意字符|
|\w|匹配字母或数字或下划线或汉字|
|\d|匹配数字|
|\s|匹配任意的空白符|
|\b|匹配单词的开始或结束|
|^|匹配字符串的开始|
|$|匹配字符串的结束|

**重复限定位（特殊字符）**

|字符|含义|
|:-:|:-|
|*|零次或以上|
|+|一次或以上|
|?|零次或一次|
|{n}|重复n次|
|{n,}|重复n次或以上|
|{n,m}|重复n到m次|
|[abc]|匹配其中的任意字符|

### 匹配模式
**简单模式**
由简单的字符所构成的，比如 `/abc/`，仅会匹配一个连续的abc字符串
**特殊字符模式**
当需要匹配一个不确定的字符串时，比如abbbc，其中b可以出现很多次，这时可以用特殊字符(重复限定位)`+`，表示匹配一次或以上，比如`/ab+c/`

### 正则方法
**1、test()用于检测一个字符串是否匹配某个模式，如果匹配则返回true**
> 语法：reg.test(str)  &emsp;用reg规则去测试str中是否有符合reg规则的部分
```javascript
  let reg = /abc/                   //规则是abc字符串
  console.log(reg.test("abcd"));    //true
```
**2、replace()字符串替换**
> 语法：str.replace(reg,newStr)  &emsp;用newStr去替换str字符串中reg匹配的部分，返回值是替换后的新字符串
```javascript
  let reg = /abc/
  console.log("abcdef".replace(reg,"xxx"));   //用"xxx"替换"abcdef"中的"abc"，所以输出xxxdef
```
**3、search()用于检索字符串中指定字符串，并返回子串的起始位置**
> 语法：str.search(reg)  &emsp;在str中搜索符合reg规则的字符串，并返回起始位置，没找到返回-1
```javascript
  let reg = /abc/
  console.log("defabc".search(reg));    //3  "abc"在"defabc"中首次出现的位置是3
```
**4、match()在字符串内检索指定的值，找到一个或多个正则表达式匹配，返回首次匹配内容的数组，没匹配到返回null**
> 语法：str.match(reg)  &emsp;用reg规则去匹配str中符合规则的字符串，并返回首个匹配内容的数组
```javascript
  let reg = /a/
  console.log("ababaj".match(reg));   //["a",groups: undefined,index: 0,input: "ababaj"]
  用g修饰符可以进行全局匹配
  let reg = /a/g
  console.log("ababaj".match(reg));   //["a","a","a"]  //全局匹配会输出所有匹配到的内容组成的数组
```