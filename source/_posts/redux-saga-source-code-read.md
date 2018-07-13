---
title: redux-saga-source-code-read
subtitle: "redux-saga源码试探阅读"
date: 2018-07-04 17:24:39
author: codinglobster
header-img: "redux-saga.png"
tags: 
   - react
   - redux
---
## 前言
在使用`dva`框架的过程中，我一直很好奇`effect`是如何实现将异步代码写成了同步代码的形式，直到看到了`dva知识图谱`才了解到，异步处理这块是交由`redux-saga`来处理的，所以就有了这篇试探阅读代码，来看看到底自己能不能看懂这优雅的异步处理源码。

## 关于生成器函数(generator function)

`redux-saga`的核心就是生成器函数,所以首先来弄清楚生成器函数的基本用法吧。
```js
function* generator(i) {
  yield i;
  yield i + 10;
}

var gen = generator(10);

console.log(gen.next().value);
// expected output: 10

console.log(gen.next().value);
// expected output: 20
```
生成器函数和普通函数类似，只是在`function`后跟了一个`*`号，但是不能使用`new`操作符来当构造函数使用。
看看mdn怎么介绍这个函数

>调用一个生成器函数并不会马上执行它里面的语句，而是返回一个这个生成器的 迭代器 （iterator ）对象。当这个迭代器的 next() 方法被首次（后续）调用时，其内的语句会执行到第一个（后续）出现yield的位置为止，yield 后紧跟迭代器要返回的值。或者如果用的是 yield*（多了个星号），则表示将执行权移交给另一个生成器函数（当前生成器暂停执行）。

因为这种特殊的机制，非常适合分步处理，但`redux-saga`到底是怎样做到的呢？

## 关于effect

源码中暴露出来的effects
```js
export {
  take,
  takeMaybe,
  put,
  putResolve,
  all,
  race,
  call,
  apply,
  cps,
  fork,
  spawn,
  join,
  cancel,
  select,
  actionChannel,
  cancelled,
  flush,
  getContext,
  setContext,
  delay,
} from './internal/io'
```

  初次接触effect的时候，天真的以为，saga提供的函数会直接运行，并将结果返回，但某日查看官方文档，才发现，saga是声明式effect,当我们调用effect的时候，实际上是生成了一个描述当前行为的对象，如调用
  ```js
// 调用
yield call(Api.fetch, '/products')
// 返回结果
  {
  CALL: {
    fn: Api.fetch,
    args: ['./products']  
  }
}
```


