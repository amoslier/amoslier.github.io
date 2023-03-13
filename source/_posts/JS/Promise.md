---
title: Promise
categories:
  - [JavaScript]
tags: 
---

# Promise

## 概念

- 处理异步请求
- 单向状态更变
- 回调地狱

## 应用场景

Promise 来解决反馈结果需要等待的场景

## 同步与异步

### 同步

同步是指当发起一个请求时，如果未得到请求结果，代码逻辑将会等待，直到结果出来为止才会继续执行之后的代码。

### 异步

异步是指当发起一个请求时，不会等待请求结果，直接继续执行后面的代码。请求结果的处理逻辑，会添加一个监听，等到反馈结果出来之后，在回调函数中处理对应的逻辑。

## 原生AJAX

- 创建 xhr ( XML Http Requst )
- 建立连接，xhr.open()
- 发送请求，xhr.send()
- 进行监听，xhr.onReadyStateChange()

倘若我们在，需要在监听中，拿到数据，接着作为参数继续去请求，那么就开始出现，回调的嵌套，如若回调愈加愈多，就会形成可怕的回调地狱。

promise 以及其 then 的出现就是来度化此劫的。

## 核心

resolve、reject进行状态更新pedding -- resolved / reject

resolve、reject 是 executor 函数（执行器）的两个参数。他们能够将请求结果的具体数据传递出去。

- then

Promise实例拥有的 then 方法，用来处理当请求结果的状态变成 resolved 时的逻辑。

then 的第一个参数也为一个回调函数，该函数的参数则是 resolve 传递出来的数据。

then 的回调函数的返回值也会作为参数，向下传递。

- catch

catch 方法，用来处理当请求结果的状态变成 rejected 时的逻辑。

catch 的第一个参数也为一个回调函数，该函数的参数则是 reject 传递出来的数据。

- promise.all

进行批处理异步请求，可以接受一个 promise 数组。

当所有的 promise 全部为 resolved / rejected 才返回 resolved / rejected。

- promise.race

进行批处理异步请求，可以接受一个 promise 数组。

当有一个的 promise 为 resolved / rejected 就返回 resolved / rejected。

## then的执行机制

then 必须在 promise 的状态确定后才能被执行，并将其回调函数，放入微任务队列，等待执行。

注意：当 then 返回 promise 时，会产生两个微任务。一次是因为返回的 promise 作为参数，需要直接调用 then 得到其 promise 状态，另一个是其内部自己产生的。

```js
let p1 = Promise.resolve()
 .then(function f1(v) { console.log(1) })
 .then(function f2(v) { console.log(2) })
 .then(function f3(v) { console.log(3) })

p1.then(function f4(v) { console.log(4) })
p1.then(function f5(v) { console.log(5) })
let p2 = Promise.resolve()
 .then(function f11(v) { console.log(11) })
 .then(function f22(v) { console.log(22) })
 .then(function f33(v) { console.log(33) })

p2.then(function f44(v) { console.log(44) })
p2.then(function f55(v) { console.log(55) })
// 1 11 2 22 3 33 4 5 44 55
```

### 总结

1. Promise的状态一经改变就不能再改变。
2. .then和.catch都会返回一个新的Promise。
3. catch不管被连接到哪里，都能捕获上层未捕捉过的错误。
4. 在Promise中，返回任意一个非 promise 的值都会被包裹成 promise 对象，例如return 2会被包装为return Promise.resolve(2)。
5. Promise 的 .then 或者 .catch 可以被调用多次, 但如果Promise内部的状态一经改变，并且有了一个值，那么后续每次调用.then或者.catch的时候都会直接拿到该值。
6. .then 或者 .catch 中 return 一个 error 对象并不会抛出错误，所以不会被后续的 .catch 捕获。
7. .then 或 .catch 返回的值不能是 promise 本身，否则会造成死循环。
8. .then 或者 .catch 的参数期望是函数，传入非函数则会发生值透传。
9. .then方法是能接收两个参数的，第一个是处理成功的函数，第二个是处理失败的函数，再某些时候你可以认为catch是.then第二个参数的简便写法。
10. .then 方法回调函数，返回 promise，会额外产生两次微任务。
11. .finally方法也是返回一个Promise，他在Promise结束的时候，无论结果为resolved还是rejected，都会执行里面的回调函数。
12. async 函数一定会返回一个 promise 对象。如果一个 async 函数的返回值看起来不是 promise，那么它将会被隐式地包装在一个 promise 中。
13. 在 async 函数中抛出了错误，则终止错误结果，不会继续向下执行，可以通过 try catch 或者 .catch 使得错误的地方不影响。
14. await 后面的语句相当于放到了 new Promise 中，下一行之后的代码都放到 Promise.then 中，需要等待 await 后面 promise 的处理。

## 运用

- 手写 promise
  
  - executor
    
    - 绑定 resolve reject 中的 this
  
  - resolve 状态更变
  
  - reject 状态更变
  
  - then
    
    - 判断传入参数是不是函数
    
    - 返回新的 promise
      
      - 创建成功、失败的回调，其中执行传入的函数，加入微任务队列中
      
      - 根据 参数函数执行的返回值，进行最后的 resolve
        
        - 返回值是 promise 直接调用 then，得到 promise 的结果，继续向下传递
        
        - 返回值不是 promise，直接 resolve
      
      - 判断当前状态，整合 map 进行对
  
  - catch

- promise 实现循环异步事件

```js
function fn(index, timeout) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      console.log(index);
      resolve();
    }, timeout);
  });
}

async function light() {
  await fn(1, 1000);
  await fn(2, 2000);
  await fn(3, 3000);
  light();
  // const promise = Promise.resolve();
  // promise
  //   .then(() => {
  //     return fn(1, 1000);
  //   })
  //   .then(() => {
  //     return fn(2, 2000);
  //   })
  //   .then(() => {
  //     return fn(3, 3000);
  //   })
  //   .then(() => {
  //     return light();
  //   });
}

light();
```




- promise.all 结果收集不考虑其是否成功或失败

```js
function mergePromise(promiseArr) {
  let data = [];
  let promise = Promise.resolve();
  promiseArr.forEach((item) => {
    promise = promise.then(item).then((res) => {
      data.push(res);
      return data;
    });
  });
  return promise;
}
```

## 事件循环机制

- 函数调用栈

- 任务队列

- 宏任务队列
  
  - script
  
  - setTimeout
  
  - setInteval
  
  - IO
  
  - render

- 微任务队列
  
  - Promise.then
  
  - process.nextTick

### 循环机制

宏任务 x 1 ➡️ 函数调用栈 ➡️ 微任务 x n ➡️ 宏任务 x 1 ➡️····

也就是说，宏任务一个一个的出队列，进入函数调用栈执行，然后在执行期间产生的微任务，会在此次函数调用栈执行完毕后，去清空微任务队列，以此往复。

## 参考

[详解ES6中的async/await - 腾讯云开发者社区-腾讯云](https://cloud.tencent.com/developer/article/1623173)

[【建议星星】要就来45道Promise面试题一次爽到底(1.1w字用心整理) - 掘金](https://juejin.cn/post/6844904077537574919)
