---
title: Promise
categories:
  - [JavaScript,核心]
tags: 
---

# Promise

```js
// 简单的ajax原生实现
var url = 'https://hq.tigerbrokers.com/fundamental/finance_calendar/getType/2017-02-26/2017-06-10';
var result;

var XHR = new XMLHttpRequest();
XHR.open('GET', url, true);
XHR.send();

XHR.onreadystatechange = function() {
    if (XHR.readyState == 4 && XHR.status == 200) {
        result = XHR.response;
        console.log(result);
    }
}
```

在ajax的原生实现中，利用了onreadystatechange事件，当该事件触发并且符合一定条件时，才能拿到想要的数据，之后才能开始处理数据

但是此时如果我们需要一个新的ajax请求的，并且这个新请求需要，上一个请求的结果，那么我们就需要这样：

```javascript
var url = 'https://hq.tigerbrokers.com/fundamental/finance_calendar/getType/2017-02-26/2017-06-10';
var result;

var XHR = new XMLHttpRequest();
XHR.open('GET', url, true);
XHR.send();

XHR.onreadystatechange = function() {
    if (XHR.readyState == 4 && XHR.status == 200) {
        result = XHR.response;
        console.log(result);

        // 伪代码
        var url2 = 'http:xxx.yyy.com/zzz?ddd=' + result.someParams;
        var XHR2 = new XMLHttpRequest();
        XHR2.open('GET', url, true);
        XHR2.send();
        XHR2.onreadystatechange = function() {
            ...
        }
    }
}
```

如果继续向下，代码就变成一坨狗屎了，往往也被称为**回调地狱**

届时，Promise，站出来解决这个问题了

除了解决 **回调地狱** ，还有更为显著的效果，**为了代码更加具有可读性和可维护性，我们需要将数据请求与数据处理明确的区分开来**

当然执行顺序还可以通过 函数调用栈、任务队列 来调控

```javascript
// 一个简单的封装
function want() {
    console.log('这是你想要执行的代码');
}

// 函数调用栈
function fn(want) {
    console.log('这里表示执行了一大堆各种代码');

    // 其他代码执行完毕，最后执行回调函数
    want && want();
}

fn(want);

//队列
function want() {
    console.log('这是你想要执行的代码');
}

function fn(want) {
    // 将想要执行的代码放入队列中，根据事件循环的机制，我们就不用非得将它放到最后面了，由你自由选择
    want && setTimeout(want, 0);
    console.log('这里表示执行了一大堆各种代码');
}

fn(want);
```

Promise

```javascript
function want() {
    console.log('这是你想要执行的代码');
}

function fn(want) {
    console.log('这里表示执行了一大堆各种代码');

    // 返回Promise对象
    return new Promise(function(resolve, reject) {
        if (typeof want == 'function') {
            resolve(want);
        } else {
            reject('TypeError: '+ want +'不是一个函数')
        }
    })
}

fn(want).then(function(want) {
    want();
})

// 错误处理
fn('1234').catch(function(err) {
    console.log(err);
})
```

#### Promise的三种状态

- pending：等待，或者正在进行，未得到结果
- resolved（Fulfilled）：已经完成，并得到预期结果，可继续执行
- rejected：得到非预期结果，拒绝执行

Promise状态不受外界环境影响，只能从 peding -> resolved / rejected，且不可逆

一、在Promise对象的构造函数中，将一个函数作为第一个参数。而这个函数，就是用来处理Promise的状态变化。

```javascript
new Promise(function(resolve, reject) {
    if(true) { resolve() };
    if(false) { reject() };
})
```

二、`Promise.then(resolve, reject)`，接受构造函数中，处理的状态，分别对应执行

```javascript
function fn(num) {
    return new Promise(function(resolve, reject) {
        if (typeof num == 'number') {
            resolve();
        } else {
            reject();
        }
    }).then(function() {
        console.log('参数是一个number值');
    }, function() {
        console.log('参数不是一个number值');
    })
}

fn('hahha');
fn(1234);
```

then方法的执行结果也会返回一个Promise对象。因此我们可以进行then的链式执行，这也是解决**回调地狱**的主要方式。

```javascript
function fn(num) {
    return new Promise(function(resolve, reject) {
        if (typeof num == 'number') {
            resolve();
        } else {
            reject();
        }
    })
    .then(function() {
        console.log('参数是一个number值');
    })
    .then(null, function() {
        console.log('参数不是一个number值');
    })
}

fn('hahha');
fn(1234);
```

> then(null, function() {}) 就等同于catch(function() {})