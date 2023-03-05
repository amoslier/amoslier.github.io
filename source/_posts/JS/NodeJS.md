---
title: NodeJS 基础
categories:
  - [JavaScript,NodeJS]
tags: 
---

### Node.js REPL

> 注意：REPL 也被称为运行评估打印循环，是一种编程语言环境（主要是控制台窗口），它使用单个表达式作为用户输入，并在执行后将结果返回到控制台。

```
// Windows Terminal or cmd
// 终端输入node , REPL 正在等待输入一些 JavaScript 代码
> node
>

// 可通过node直接执行文件
> node app.js 
```

<br/>

### 点命令

- .help: 显示点命令的帮助。
- .editor: 启用编辑器模式，可以轻松地编写多行 JavaScript 代码。当处于此模式时，按下 ctrl-D 可以运行编写的代码。
- .break: 当输入多行的表达式时，输入 .break 命令可以中止进一步的输入。相当于按下 ctrl-C。
- .clear: 将 REPL 上下文重置为空对象，并清除当前正在输入的任何多行的表达式。
- .load: 加载 JavaScript 文件（相对于当前工作目录）。
- .save: 将在 REPL 会话中输入的所有内容保存到文件（需指定文件名）。
- .exit: 退出 REPL（相当于按下两次 ctrl-C）。

<br/>

### 命令行接收参数

```
// Terminal
> node app.js 1,2,3
> node app.js name=JOEOP
> node app.js --name=JOEOP
```

```javascript
// app.js
// > node app.js 1,2,3
console.log(process.argv.slice(2));  // [ '1,2,3' ]
// > node app.js name=JOEOP
console.log(process.argv.slice(2));  // [ 'name=JOEOP' ]
```

此时则 args[0] 是 name=joe，需要对其进行解析。 最好的方法是使用 minimist 库，该库有助于处理参数：

`npm install minimist`

```javascript
// app.js
// > node app.js --name=JOEOP
console.log(args['name']); // JOEOP
```

### console.log

`console.log('我的%s已经%d岁', '猫', 2)`打印输出

- %s 会格式化变量为字符串
- %d 会格式化变量为数字
- %i 会格式化变量为其整数部分
- %o 会格式化变量为对象

`console.clear()`清空控制台

`console.count()`count 方法会对打印的字符串的次数进行计数，并在其旁边打印计数：

```javascript
const oranges = ['橙子', '橙子']
const apples = ['苹果']
oranges.forEach(fruit => {
  console.count(fruit)
})
apples.forEach(fruit => {
  console.count(fruit)
})
// 橙子: 1
// 橙子: 2
// 苹果: 1
```

`console.trace()`打印堆栈踪迹

```javascript
const function2 = () => console.trace()
const function1 = () => function2()
function1()
```

`console.time() console.timeEnd()`计算耗时

```javascript
const doSomething = () => console.log('测试')
const measureDoingSomething = () => {
  console.time('doSomething()')
  doSomething()
  console.timeEnd('doSomething()')
}
measureDoingSomething()
```

`npm install progress`创建进度条

```javascript
const ProgressBar = require('progress')
const bar = new ProgressBar(':bar', { total: 10 })
const timer = setInterval(() => {
  bar.tick()
  if (bar.complete) {
    clearInterval(timer)
  }
}, 100)
```

***

### NodeJS

- Node是对ES标准的一个实现，Node也是一个JS引擎
- Node可以在后台编写服务器
  - Node编写服务器都是单线程服务器
    - 进程 -- 一个个的工作计划（工厂中的车间）
    - 线程 -- 计算机最小的运算单位（工厂中的工人）
- 传统的服务器都是多线程的
  - 每进来一个请求，就创建一个线程去处理请求
- Node的服务器单线程的
  - Node处理请求时是单线程，但是在后台拥有一个I/O线程池

### NodeJS模块化

- Node verison 13.2.0 起开始正式支持 ES Modules 特性
- 之前全部采用 CommonJS 进行模块化
- Node 在引用时，会向上一层一层的寻找`node_modules`
- ES6 Modules
  - `export & import`
- CommonJS Modules
  - `module.exports & require`

### npm

- `npm -v`
- `npm search packageName`
- `npm install` 安装依赖
- `npm install/i packageName`
- `npm install/i packageName --save` 安装并添加依赖
- `npm install/i packageName -g` 全局安装
- `npm remove/r packageName`

### Buffer

- Buffer 结构用法类似数组
- 数组中不能存储二进制的文件，Buffer是专门来存储二进制数据的 `[ 00 - ff ]`
- Buffer中存储的都是二进制数据，但是显示的时候会以16进制显示
- `<buffer>.length` 占用内存大小
- `Buffer.from(str)` 字符串转`buffer`
- ~~`new Buffer(bitSize)`~~
- `Buffer.alloc(bitSize)`
- `Buffer.allocUnsafe(bitSize)` 创建指定大小的`buffer`，但其中可能含有敏感数据
- `Buffer.compare(buf1, buf2)`

### FS（File System）

- `require("fs")`
- **同步** 有返回值
- `fd = fs.openSync(path, flag[, mode])` 同步打开文件 
- `fs.writeSync(fd, str[, startIndex[, encoding]])`
- `fs.closeSync(fd)`
- **异步** 没有返回值，通过回调函数返回
- `fs.open(path, flag [, mode], callback)`
  - `callback = function(err, fd) {}` 
- `fs.write(fd, string[, position[, encoding]], callback)`