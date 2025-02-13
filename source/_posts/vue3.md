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

### provide、inject
**vue2**
父组件provide是一个对象，设置key和value子组件inject是个数组，每个元素就是provide对象的key
```javascript
// 父
provide:{
  msg: "给子组件的数据"
}
// 子
inject:['msg']
```
**vue3**
父组件provide是个函数调用，传递两个值，代表key和value，value是可选参数
子组件inject也是函数调用，同样传两个参数，代表接收的key和给这个key设置默认值，默认值是可选参数，用于父组件没有传递value值的情况
在父组件中使用ref或者reactive来使数据具有响应式，传递给子组件仍然具有响应式
```javascript
setup(){
  // 父
  provide("msg","给子组件的数据")
  // 子
  const msg = inject("msg","默认值")
}
```

### 插槽

1. 具名插槽
   > 给在子组件中的 slot 标签，添加一个名字，在父组件中就可以精准的控制哪一个 slot 显示什么内容，一般用于有多个插槽的情况

```javascript
// 父组件
<SonCom>
  {/* 使用具名插槽，必须用template包裹要传递东西 */}
  <template #son1>
    <div>传给第一个slot的值</div>
  </template>
  <template #son2>
    <div>传给第二个slot的值</div>
  </template>
</SonCom>

// 子组件
<div>
  <p>子组件</p>
  <slot name='son2'></slot>
  <slot name='son1'></slot>
</div>
```

2. 作用域插槽
   > 将子组件中的数据通过插槽传递给父组件

```javascript
// 父组件
<SonCom>
  {/* 使用作用域插槽接收数据必须用template标签 */}
  <template #son1="data1">
    <div>子组件值：{{ data1.obj1.name }}</div>
  </template>
  {/* 解构 */}
  <template #son2="{obj2}">
    <div>子组件值：{{ obj2.name }}</div>
  </template>
</SonCom>

// 子组件
<div>
  <p>子组件</p>
  <slot name='son2' :obj2='obj2'></slot>
  <slot name='son1' :obj1='obj1'></slot>
</div>

<script>
  export default{
    data() {
      return {
        obj1: {
          name: 'zs'
        },
        obj2: {
          name: 'ls'
        }
      }
    }
  }
</script>
```

### vue生命周期等待异步函数
在vue2和vue3中，支持在生命周期函数中，使用async和await来执行异步函数
```javascript
// vue2
method:{
  async myfn(){
    console.log('3');
    await new Promise((resolve, reject) => {
      console.log('4');
      setTimeout(() => {
        console.log('5');
        resolve()
      }, 2000);
    })
  }
}
async created() {
  console.log('1');
  await myfn()
  console.log('2');
},
// 打印结果 1、3、4、5、2

// vue3 假设是setup语法糖
const myfn = async()=>{
  console.log('3');
  await new Promise((resolve, reject) => {
   console.log('4');
   setTimeout(() => {
     console.log('5');
     resolve()
   }, 2000);
 })
}
onMounted(async() => {
  console.log('1');
  await myfn()
  console.log('2');
}),
// 打印结果 1、3、4、5、2
```
**vue3 setup语法糖中，顶层不能直接使用await，会导致页面白屏，只能放在其他的生命周期中，比如onMounted**

### vue3 Api

**watchEffect**
立即运行一个函数，同时响应式地追踪其依赖，并在依赖更改时重新执行。需要追踪的依赖必须是响应式的，而且必须放在异步代码之前。
```javascript
// 基本使用
const test = ref(1)
watchEffect(()=>{
  console.log(test.value) // 当test.value变化，就会重新执行这个副作用函数
})

// 停止监听副作用。副作用函数会返回一个函数，用于停止监听
const stop = watchEffect(()=>{
  console.log(test.value)
})
stop() // 这样就停止监听了

// 清除上一次的副作用，就是在重新执行副作用之前，会先调用一个函数，在这个函数里面手动做一些操作来去除上一次副作用可能带来的影响
watchEffect((callback)=>{ // 接收一个函数，每次副作用执行前都会调用一次，函数名随意写都可以
  console.log(test.value) // 指定依赖
  const timer = setTimeout(()=>{
    test.value++ // 这是错误的示范，不能在副作用里面去修改依赖项，这样会死循环，这里是为了方便说明清除副作用的用法
  },1000)
  callback(()=>{
    // 手动做一些处理
    timer && clearTimeout(timer) // 每次副作用执行前清除上一次的定时器，避免因为除了定时器以外的其他操作造成开了很多定时器的问题
  })
})
```

**toRaw**
返回一个由vue创建的代理对象的原始对象，就是将响应式对象转换为普通对象，不改变源数据
```javascript
// 1. 转换由reactive创建的数据
const obj = reactive({ a: 1 })
const objRaw = toRaw(obj)
// 这个objRaw就是一个普普通通的对象了

// 2. 装换由ref创建的数据
const obj1 = ref({ a: 1 })
const objRaw1 = toRaw(obj.value)
// 一样的效果，只是转换时必须要用.value
// 注意：如果ref创建的数据是普通数据类型这样转换无意义，因为用.value获取到数据不是一个响应式对象，而是一个普通值
```

**markRaw**
将一个普通对象标记为不可转换为代理对象，就是无法让这个普通对象变成响应式了
```javascript
const obj = { a: 1 }
const objRaw = markRaw(obj)
// 此时这个objRaw就被标记为不可转为代理了，这个对象上会多一个'__v_skip'属性，就是用这个来标记的

const objNew = reactive(objRaw) // 即使用了reactive来设置代理，也不会生效了，仍然是一个普通对象
const objNew1 = ref(objRaw) // 使用ref也同样无效，不具有响应式

// 注意：markRaw是作用于普通对象的，对一个代理对象使用无效
const obj1 = ref({ a: 1 })
const obj1Raw = markRaw(obj1.value) // obj1Raw仍然是一个代理对象，使用reactive去定义obj1也是一样的
```

**shallowRef**
ref的浅层作用形式。就是使用ref创建的数据，不论数据结构有多深，全部都是代理对象，具有响应式的，而shallowRef只会代理第一层，也就是只有直接.value这一层才会有响应式。
如果创建的是普通数据类型，这两个api没区别，只有创建引用数据类型才有区别
```javascript
const obj = shallowRef({ a: 1 }) // 这时用obj.value.a去修改值，是没有响应式的，除非直接替换obj.value。如果用的是ref，那这两种方式都是会有响应式
```

**triggerRef**
强制触发一个依赖于shallowRef的副作用。就是当修改了shallowRef创建的引用数据类型数据，本来是没有响应式的，页面不会变化，但通过调用这个api，可以让页面更新
```javascript
const obj = shallowRef({ a: 1 })
obj.value.a = 2 // 数据变化了但是页面无任何变化
triggerRef(obj) // 此时页面就更新了，相当于是使用最新的数据重新渲染一下页面
```

**shallowReactive**
reactive的浅层作用形式，和shallowRef同理
```javascript
const obj = shallowReactive({
  a: 1,
  obj1: {
    b: 1
  }
})
obj.obj1.b = 2 // 数据变化了但是页面无任何变化
// 只有第一层的属性变化才会有响应式，比如：
obj.a = 2
obj.obj1 = { b: 2 }
```

**toRef**
基于响应式对象上的一个属性，创建一个对应的 ref。这样创建的 ref 与其源属性保持同步：改变源属性的值将更新 ref 的值，反之亦然。
```javascript
const obj = reactive({
  a: 1,
  b: 1
})
const aa = toRef(obj,"a") // 将obj中的a属性使用ref来创建，此时修改obj.a或者aa.value，双方数据都会变化

// 这和直接用ref有区别
const aaa = ref(obj.a) // 这时obj.a和aaa.value无任何关联，各改各的
```

**toRefs**
将响应式对象的所有属性都转为ref的值，相当于是批量使用了toRef