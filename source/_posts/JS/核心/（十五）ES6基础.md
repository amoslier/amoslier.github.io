---
title: Es6
categories:
  - [JavaScript,核心]
tags: 
---

# ES6

[babel在线编译工具](http://babeljs.io/repl/)

### let/const

- 块级作用域
- 不具备变量提升
  - 可以说成是声明式提升，赋值并没有提升，因此存在暂时性死区
  - 因为赋值的提升并没有完成，所以即使声明提升了也不能够去使用
- const变量定义的引用类型，如对象，对象的引用不可修改，但对象中的属性可以修改

### 箭头函数

`() => {}`

> 箭头函数可以替换函数表达式，但是不能替换函数声明

**箭头函数没有this，**，如果你在箭头函数中使用this，那么this一定是外层的this

```javascript
const person = {
    name: 'tom',
    getName: () => this.name
}

person.getName();  // Cannot read properties of undefined (reading 'name')

const person = {
    name: 'tom',
    getName: function() {
      return () => this.name
    }
}
person.getName()(); // tom
```

> 在ES6中，会默认采用严格模式，因此this也不会自动指向window对象了，而箭头函数本身并没有this，因此this就只能是undefined，这一点，在使用的时候，一定要慎重慎重再慎重，不然踩了坑你都不知道自己错在哪！这种情况，如果你还想用this，就不要用使用箭头函数的写法。

**箭头函数中无法访问`arguments`**

### 模板字符串

```javascript
// es6
const a = 20;
const b = 30;
const string = `${a}+${b}=${a+b}`;
```

### 解析结构

```javascript
// 首先有这么一个对象
const props = {
    className: 'tiger-button',
    loading: false,
    clicked: true,
    disabled: 'disabled'
}
```

```javascript
// es5
var loading = props.loading;
var clicked = props.clicked;

// es6
const { loading, clicked } = props;

// 给一个默认值，当props对象中找不到loading时，loading就等于该默认值
const { loading = false, clicked } = props;
```

数组的解构赋值

```javascript
// es6
const arr = [1, 2, 3];
const [a, b, c] = arr;
```

数组以序列号一一对应，这是一个有序的对应关系
而对象根据属性名一一对应，这是一个无序的对应关系
根据这个特性，使用解析结构从对象中获取属性值更加具有可用性

### 函数默认参数

```javascript
function add(x = 20, y = 30) {
    return x + y;
}

console.log(add());
```

### 展开运算符

在ES6中用`...`来表示展开运算符，它可以将数组方法或者对象进行展开

```javascript
const arr1 = [1, 2, 3];
const arr2 = [...arr1, 10, 20, 30]; // [1, 2, 3, 10, 20, 30];
```

```javascript
const obj1 = {
  a: 1,
  b: 2,
  c: 3
}

const obj2 = {
  ...obj1,
  d: 4,
  e: 5,
  f: 6
}

// { a: 1, b: 2, c: 3, d: 4, e: 5, f: 6 }
```

利用展开运算符来处理剩余的数据

```javascript
// 这种方式在react中十分常用
const props = {
  size: 1,
  src: 'xxxx',
  mode: 'si'
}


const { size, ...others } = props;

console.log(others) // { src: 'xxxx', mode: 'si' }

// 然后再利用暂开运算符传递给下一个元素，再以后封装react组件时会大量使用到这种方式，正在学习react的同学一定要搞懂这种使用方式
<button {...others} size={size} />
```

处理函数不定参

```javascript
// 所有参数之和
const add = (a, b, ...more) => {
    return more.reduce((m, n) => m + n, a + b)
}

console.log(add(1, 23, 1, 2, 3, 4, 5)) // 39
```

### 对象字面量和class

#### 对象字面量

- 属性值同名省略
- 对象中的属性可以采用`[params]`，实现**变量**属性名
  - 在ant-design的源码实现中，就大量使用了这种方式来拼接当前元素的className，例如:
    
    ```javascript
    let alertCls = classNames(prefixCls, {
          [`${prefixCls}-${type}`]: true,
          [`${prefixCls}-close`]: !this.state.closing,
          [`${prefixCls}-with-description`]: !!description,
          [`${prefixCls}-no-icon`]: !showIcon,
          [`${prefixCls}-banner`]: !!banner,
     }, className);
    ```

#### class

```javascript
// ES5
// 构造函数
function Person(name, age) {
  this.name = name;
  this.age = age;
}

// 原型方法
Person.prototype.getName = function() {
  return this.name
}

// ES6
class Person {
  constructor(name, age) {  // 构造函数
    this.name = name;
    this.age = age;
  }

  getName() {  // 原型方法
    return this.name
  }
}

```

> babel 会将ES6的写法编译成为利用`object.defineProperty`方式实现
> `Object.defineProperty()` 方法会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性，并返回此对象。

实际开发中还有几种写法方式不同

```javascript
class Person {
  constructor(name, age) {  // 构造函数
    this.name = name;
    this.age = age;
  }

  getName() {   // 方法添加到原型中
    return this.name
  }

  static a = 20;  // 相当于类的私有属性

  c = 20;   // 属性添加到构造函数中，相当于 this.c = 20

  getAge = () => this.age   //方法添加到构造函数，等同this.getAge = function() {}

}

```

箭头函数还是要注意**this**指向问题

**继承extends**

```javascript
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  getName() {
    return this.name
  }
}

// Student类继承Person类
class Student extends Person {
  constructor(name, age, gender, classes) {
    super(name, age); // super 必须在this前面使用，否则报错
    this.gender = gender;
    this.classes = classes;
  }

  getGender() {
    return this.gender;
  }
}
```

> `super(name, age) === Person.call(this)`
> 
> super还可以直接调用父级的原型方法，`super.getName()`
