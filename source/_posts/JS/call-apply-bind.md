---
title: call-apply-bind
categories:
  - [JavaScript]
tags: 
---

### call-apply-bind

#### 区别

- 都是进行 this 绑定的。

- call 参数一个个传递。

- apply 参数通过数组传递。

- bind 返回一个新的函数，并不立即执行函数。

#### 手写

##### call

```js
/**
 * call
 * @param {Object} context
 * 主要思路就是在传进来的this主体中增加 fn
 * 在通过 this.fn() 执行
 */
Function.prototype.call = function (context) {
  if (typeof this !== "function") return;
  that = context || window;
  that.fn = this;
  let res;
  res = that.fn(...[...arguments].slice(1));
  delete that.fn;
  return res;
};
```

##### apply

```js
/**
 * apply
 * @param {Object} context
 */
Function.prototype.apply = function (context) {
  if (typeof this !== "function") return;
  that = context || window;
  that.fn = this;
  let res;
  res = that.fn(...arguments[1]);
  delete that.fn;
  return res;
};
```

##### bind

```js
/**
 * bind
 * @param {Object} context
 * @returns Function
 *
 * 需要注意的是，返回的函数也可能被作为 构造函数执行，此时绑定this就没有作用了。
 */

Function.prototype.bind = function (context) {
  const fn = this;
  const newFun = function () {
    if (this instanceof newFun) fn.apply(this, [...arguments]);
    else fn.apply(context, [...arguments]);
  };
  return newFun;
};
let a = {
  n: 1,
};
```
