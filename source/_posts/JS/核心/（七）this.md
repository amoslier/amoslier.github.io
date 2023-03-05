---
title: this
categories:
  - [JavaScript,核心]
tags: 
---

# this

![](https://s2.loli.net/2023/03/05/gAQwalibW9X1nK3.webp)

**this的指向，是在函数被调用的时候被确定的，也就是执行上下文被创建时确定的**

同一个函数，调用方式不同，this指向也不同

```javascript
var a = 10;
var obj = {
  a: 20
}

function fn() {
  console.log(this.a);
}

fn(); // 10 window调用
fn.call(obj); // 20 call指定this为obj
```

**在函数执行过程中，this一旦被确定，就不可更改了**

```javascript
var a = 10;
var obj = {
  a: 20
}

function fn() {
  this = obj; // 这句话试图修改this，运行后会报错
  console.log(this.a);
}

fn();
```

### 全局对象中的this

全局环境中的this。指向它本身

```javascript
// 通过this绑定到全局对象
this.a2 = 20;

// 通过声明绑定到变量对象，但在全局环境中，变量对象就是它自身
var a1 = 10;

// 仅仅只有赋值操作，标识符会隐式绑定到全局对象
a3 = 30;

// 输出结果会全部符合预期
console.log(a1);
console.log(a2);
console.log(a3);
```

```javascript
// demo03
var a = 20;
var obj = {
  a: 10,
  c: this.a + 20,
  fn: function () {
    return this.a;
  }
}

console.log(obj.c);  // 40 
console.log(obj.fn());  // 10
```

在函数上下文中，this由调用者提供，由调用函数的方式来决定。**严格模式下，如果调用者函数，被一个对象所拥有，那么该函数在调用时，内部的this指向该对象。如果函数独立调用，那么该函数内部的this指向undefined。但在非严格模式，当this指向undefined时，this会指向全局对象**

demo03

单独的`{}`不会形成新的作用域，因此这里的`this.a`，由于并没有作用域的限制，它仍然处于全局作用域之中。所以这里的this其实是指向的window对象。

```javascript
'use strict';
var a = 20;
function foo() {
  var a = 1;
  var obj = {
    a: 10,
    c: this.a + 20,
    fn: function () {
      return this.a;
    }
  }
  return obj.c;
}
console.log(foo());    // TypeError: Cannot read properties of undefined (reading 'a')
console.log(window.foo());  // 40
```

严格模式this指向undefined

**注意，无论是否严格模式，全局声明的变量都会变成window对象上的属性**

```javascript
var a = 20;
var foo = {
  a: 10,
  getA: function () {
    return this.a;
  }
}
console.log(foo.getA()); // 10

var test = foo.getA;
console.log(test());  // 20
```

```javascript
var a = 20;
function getA() {
  return this.a;
}
var foo = {
  a: 10,
  getA: getA
}
console.log(foo.getA());  // 10
```

### 使用call、apply显示指定this

`function.call(object, params, params, ...)`

`function.apply(object, [params, params, ...])`

```javascript
function fn(num1, num2) {
  console.log(this.a + num1 + num2);
}
var obj = {
  a: 20
}

fn.call(obj, 100, 10); // 130
fn.apply(obj, [20, 10]); // 50
```

使用场景：

#### 将类数组对象转换为数组

```javascript
function exam() {
  var arg = [].slice.call(arguments);
  console.log(arg);
}

exam(2, 8, 9, 10, 3);
// [ 2, 8, 9, 10, 3 ]
```

#### 根据自己的需要灵活修改this指向

```javascript
var foo = {
  name: 'joker',
  showName: function () {
    console.log(this.name);
  }
}
var bar = {
  name: 'rose'
}
foo.showName.call(bar);
```

#### 实现继承

```javascript
// 定义父级的构造函数
var Person = function (name, age) {
  this.name = name;
  this.age = age;
  this.gender = ['man', 'woman'];
}

// 定义子类的构造函数
var Student = function (name, age, high) {
  Person.call(this, name, age);
  this.high = high;
}
Student.prototype.message = function () {
  console.log('name:' + this.name + ', age:' + this.age + ', high:' + this.high + ', gender:' + this.gender[0] + ';');
}

new Student('xiaom', 12, '150cm').message();

// result
// name:xiaom, age:12, high:150cm, gender:man;
```

在Student的构造函数中，借助call方法，将父级的构造函数执行了一次，相当于将Person中的代码，在Sudent中复制了一份，其中的this指向为从Student中new出来的实例对象。call方法保证了this的指向正确，因此就相当于实现了继承。Student的构造函数等同于下。

```javascript
var Student = function (name, age, high) {
  this.name = name;
  this.age = age;
  this.gender = ['man', 'woman'];
  // Person.call(this, name, age); 这一句话，相当于上面三句话，因此实现了继承
  this.high = high;
}
```

#### 在向其他执行上下文的传递中，确保this的指向保持不变

```javascript
var obj = {
  a: 20,
  getA: function () {
    setTimeout(function () {
      console.log(this.a)
    }, 1000)
  }
}

obj.getA();
```

闭包与apply方法，封装一个bind方法

```javascript
function bind(fn, obj) {
  return funcrion() {
    return fn.apply(obj,arguments);
  }
}

var obj = {
  a: 20,
  getA: function () {
    setTimeout(bind(function () {
      console.log(this.a)
    }, this), 1000)
  }
}

obj.getA();
```

同ES5中的bind方法

```javascript
var obj = {
  a: 20,
  getA: function () {
    setTimeout(function () {
      console.log(this.a)
    }.bind(this), 1000)
  }
}
```

**ES6中也常常使用箭头函数的方式来替代这种方案**

### 构造函数与原型方法上的this

```javascript
function Person(name, age) {

    // 这里的this指向了谁?
    this.name = name;
    this.age = age;   
}

Person.prototype.getName = function() {

    // 这里的this又指向了谁？
    return this.name;
}

// 上面的2个this，是同一个吗，他们是否指向了原型对象？

var p1 = new Person('Nick', 20);
p1.getName();
```

通过new操作符调用构造函数，会经历以下4个阶段。

- 创建一个新的对象；
- 将构造函数的this指向这个新对象；
- 指向构造函数的代码，为这个对象添加属性，方法等；
- 返回新对象。