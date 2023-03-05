---
title: setTimeout与循环闭包
categories:
  - [JavaScript,核心]
tags: 
---

执行上下文，采用函数调用栈这种特殊的数据结构的调用特性。

setTimeout采用队列结构，页面中所有由setTimeout定义的操作，都将放在同一个队列中依次执行。

![](https://s2.loli.net/2023/03/05/opT9YwiFQBy1lnG.webp)

队列执行，需要等待函数调用栈清空之后才开始执行。即所有可执行代码执行完毕之后，才会开始执行由setTimeout定义的操作。而这些操作进入队列的顺序，则由设定的延迟时间来决定。

```javascript
setTimeout(function () {
  console.log(a);
}, 0);

var a = 10;

console.log(b);
console.log(fn);

var b = 20;

function fn() {
  setTimeout(function () {
    console.log('setTImeout 10ms.');
  }, 10);
}

fn.toString = function () {
  return 30;
}

console.log(fn);

setTimeout(function () {
  console.log('setTimeout 20ms.');
}, 20);

fn();
```

![](https://s2.loli.net/2023/03/05/hVzmswRqXW4ONy9.webp)
