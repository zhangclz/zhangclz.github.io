---
title: vue3
date: 2021-09-18
tags: 语法
categories: Vue3
description: vue3的基本语法和相比于vue2的一些修改
---
### ref和reactive的区别
![ref和reactive区别](ref_reactive.png)

**reactive使用注意事项**
当使用`reactive`声明了一个复杂数据类型（对象或数组）后，不能够直接让这个数据去等于另一个复杂数据类型，否则响应式会失效，页面不会更新
```javascript
let arr = reactive([1,2])
arr = [3,4]   //这样直接赋值arr将不再具有响应式并且页面也不会更新
正确的用法：arr[0] = 3,arr[1] = 4

或者把数组变为对象的属性，然后直接对属性赋值：
let arr = reactive({data:[1,2]})
arr.data = [3,4]

// 对象同理
let obj = reactive({name:zs})
错误：obj = {name:'ls'}
正确：obj.name = 'ls'
// 对象这样处理就行了，如果不嫌麻烦的话也可以把对象写成另一个对象的属性，然后对属性赋值

还有个最简单的方法，就是直接用ref去声明复杂数据类型，然后就可以直接赋值了
let arr = ref([1,2])
arr.value = [3,4]
因为用ref声明复杂数据类型时，ref会自动调用reactive把数据转换为proxy对象，同时这个对象属于ref的value属性。所以这就和上面自己定义一个对象的属性一样，只不过这个更简洁
```
而reactive会出现上述问题的原因是：**reactive实现响应式是通过*ES6的proxy代理对象 + reflect反射对象去操作原对象*实现的，当直接对这个对象赋值后，这个对象就不再指向proxy，而是指向你赋值的那个普通对象，所有就没有了响应式**
ref实现响应式的原理：数据劫持（和vue2一样）

### vue2、3中watch、computed的用法差别
**vue2：watch**
```javascript
// 有两种写法
data(){
  return{
    firstName: 'z'
  }
},
watch:{
  // 1.第一种写法，最简洁(前面省略了function声明)
  firstName(n,o){
    // 当firstName改变就会触发这个函数
  }

  // 如果想要使用deep和immediate参数，则使用如下写法
  firstName: {
    handler(n,o){
      // firstName发生改变就会执行handler这个函数
    },
    immediate: true,   // 立即执行一次handler函数，即使值还未发生改变
    deep: true         // 深度监听对象或数组中的值发生改变
  }


  // 第二种
  firstName: (n,o)=>{}
  // 或者
  firstName: function(n,o){}

  // 同样使用deep和immediate参数，则使用如下写法
  firstName:{
    handler: (n,o)=>{},
    immediate: true,
    deep: true
  } 
}
```
**vue3：watch**
```javascript

1.监视ref定义的一个数据
let firstName = ref('z')
let lastName = ref('s')
watch(firstName,(n,o)=>{
  // firstName发生改变执行此函数
},{immediate: true})
// 由此可见，watch接收3个参数，第一个是需要监听的值，第二个是函数，第三个是其他选项
// 由于vue3的响应式和vue2不同，所以不需要使用deep

2.监视ref定义的多个数据
watch([firstName,lastName],(n,o)=>{
  // 当数组的值任意一个值发生改变就会执行此函数
  // n，o的值也是个数组，分别对应watch第一个参数数组中的值
},{immediate:true})

3.监视reactive定义的数据
let person = reactive({
  name: 'zs',
  age: 18,
  job: {
    j1: {
      salary: 20,
    },
  },
})
watch(person,(n,o)=>{
  // 当person对象上任意属性发生改变后，都会执行此函数，并且n，o的值是相同的
},{immediate:true})

4.监视reactive定义的数据的某个属性
watch(()=>{person.age},(n)=>{
  // 当person中的age属性发生改变才会执行此函数
})

5.监视reactive定义的数据的多个属性
// 和ref类似，第一个参数用数组
watch([()=>{person.age},()=>person.job.j1.salary],(n)=>{
  // n的值对应第一个参数数组中的值
})
```

**vue2：computed**
```javascript
// 在vue2中使用computed监听数据变化，无需在data中声明
data(){
  return{
    firstName:'z',
    lastName:'s'
  },
  computed:{
    fullName: ()=>{
      return this.firstName+this.lastName
    }
    // fullName的值就是'zs'，这3个属性任意一个发生变化都会被监听到

    // 另一种写法
    fullName(){
      return this.firstName+this.lastName
    }
  }
}
```
**vue3：computed**
```javascript
let firstName = ref('z')
let lastName = ref('s')

let fullName = computed(()=>{
  return firstName+lastName
})
// 上述代码当firstName或lastName发生改变时，会重新return一个新的fullName，但对fullName赋值时,上述写法会报错
// 因为这种写法会把fullName定义为只读，所以适用于不会对这个属性进行修改的情况

// 所以需要使用以下完整的写法
let fullName = computed({
  get(){
    return firstName+lastName   //这和上面的一样，监听这两个属性变化
  },
  set(value){
    // 当直接对fullName赋值时，会先进入set函数，在这里对firstName和lastName进行处理
    // 然后再去get函数获取return的值
    // 如果在set中不进行处理，那么不管fullName赋值成为什么，get获取的值仍然是原来的值，相当于赋值无效

    // 在vue中用法也一样
  }
})
```

### 在 vue2 中使用防抖和节流，需要将方法从外部导入，然后在组件中声明方法时，使用`:`的方式，而不是直接写一个函数
```javascript
methods: {
  myFun: debounce(function() {
    // 真正的函数代码
  }, 2000);
}
```