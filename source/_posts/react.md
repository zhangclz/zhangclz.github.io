---
title: react
date: 2025-02-13 10:37:24
tags: 语法
categories: React
description : react的基本用法
---

### API

**memo**

当 props 没有改变时跳过重新渲染，用于缓存整个组件，memo不会修改该组件，而是返回一个新的、记忆化的组件。

```javascript
const Greeting = memo(function Greeting({ name }) {
  return <h1>Hello, {name}!</h1>;
});
export default Greeting;
// 当Greeting组件接收的name参数没有发生改变时，即使父组件改变了state从而触发了重新渲染，该组件也不会重新渲染。
// 但如果它自己的state或正在使用的context发生更改，组件是会重新渲染的
```

**useMemo** (hook)

它在每次重新渲染的时候能够缓存计算的结果，只缓存函数值，当依赖项没有改变，父组件重新渲染时，直接返回原函数值
```javascript
import { useMemo } from 'react';

function TodoList({ todos, tab }) {
  const visibleTodos = useMemo(
    () => filterTodos(todos, tab),
    [todos, tab]
  );
  // 当依赖项todos和tab没有改变时，父组件更新直接返回缓存的函数值
}
```
当父组件中使用了一个子组件，子组件的某个prop参数是一个函数，返回值是一个引用数据类型，当对这个子组件使用了memo后，父组件每次渲染，这个prop参数函数会重新执行，返回一个新的引用，即便内容没有改变，但引用会变，这时候memo就会无效，所以，要使用useMemo去缓存这个函数的值，才能使子组件不重新渲染。此外有些函数的计算过程很复杂，需要耗费大量的时间与资源，这时候就很有必要使用该hook去做优化了
```javascript
import { memo } from 'react';

const List = memo(function List({ items }) {
  // ...
});
export default function TodoList({ todos, tab, theme }) {
  const visibleTodos = filterTodos(todos, tab); // 每次重新渲染，会返回一个新的引用
  return (
    <div className={theme}>
      {/* 即便使用了memo，但无效 */}
      <List items={visibleTodos} />
    </div>
  );
}
// 修改
const visibleTodos = useMemo(
  () => filterTodos(todos, tab),
  [todos, tab] // ...只要这些依赖项不变，引用就不会变，子组件就不会变重新渲染了...
);

```
> 总结：使用memo时，要检查子组件接收的props，看是否有函数返回的引用数据类型，如果有就需要时用useMemo去缓存函数值。此外如果函数计算过程复杂，也应该去缓存。

**useCallback**(hook)

在多次渲染中缓存函数。通常情况下，创建函数开销并不高，所以其实并不需要在任何地方都使用这个hook去缓存函数，主要使用场景是缓存函数来跳过组件的非必要渲染。缓存的函数中的state值，也是会缓存的，如果想要在函数中获取最新的state值，需要放在依赖项里面
```javascript
import { memo } from 'react';
const ShippingForm = memo(function ShippingForm({ onSubmit }) {
  // ...
});
function ProductPage({ productId, referrer, theme }) {
  function handleSubmit(orderDetails) {
    post('/product/' + productId + '/buy', {
      referrer,
      orderDetails,
    });
  }
  // 父组件重新渲染，子组件使用了memo，检查props有没有改变，由于handleSubmit是一个函数，所以每次渲染引用地址会变，所以memo不生效
  return (
    <div className={theme}>
      <ShippingForm onSubmit={handleSubmit} />
    </div>
  );
  // 修改，使用useCallback缓存函数，只要依赖项没变，组件重新渲染会返回相同的引用地址，此时memo就能生效了
  const handleSubmit = useCallback((orderDetails) => {
    post('/product/' + productId + '/buy', {
      referrer,
      orderDetails,
    });
  }, [productId, referrer]);
}
```
> 总结：useCallback和useMemo很类似，一个用于缓存整个函数，一个用于缓存函数值，基本都用于子组件的props缓存，根据参数类型来区分使用。此外useCallback不是很需要仅仅是为了防止重复创建，而去大量使用，也可以用，但没必要


**forwardRef**

允许组件使用ref将 DOM 节点暴露给父组件，配合`useImperativeHandle`使用在父组件中就可以直接调用子组件的方法

```javascript
import { forwardRef } from 'react';

const MyInput = forwardRef(function MyInput(props, ref) {
  const { label, ...otherProps } = props;
  return (
    <label>
      {label}
      <input {...otherProps} />
    </label>
  );
});
// 将子组件dom节点暴露出去了
```

### Hook

**useImperativeHandle**

它能让你自定义由ref暴露出来的句柄，配合`forwardRef`使用，才能够在父组件中调用子组件暴露的方法
```javascript
import { forwardRef, useImperativeHandle } from 'react';

const MyInput = forwardRef(function MyInput(props, ref) {
  const myfn = () => {
    console.log('1');
  }
  useImperativeHandle(ref, () => {
    return {
      // ... 你的方法 ...
      myfn
    };
  });
})
// 将子组件的myfn方法暴露出去了
```
