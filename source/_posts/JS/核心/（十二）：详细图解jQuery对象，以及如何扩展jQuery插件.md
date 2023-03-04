---
title: 详细图解jQuery对象，以及如何扩展jQuery插件
categories:
  - [JavaScript,核心]
tags: 
---

# jQuery对象

  使用jQuery对象时，我们这样写：

```javascript
// 声明一个jQuery对象
$('.target')

// 获取元素的css属性
$('.target').css('width')
```

#### 浅析Jquery源码

```javascript
;
(function (ROOT) {

  // 构造函数
  var jQuery = function (selector) {

    // 在jQuery中直接返回new过的实例，init是jQuery的真正构造函数，存在JQuery原型上
    return new jQuery.fn.init(selector)
  }

  jQuery.fn = jQuery.prototype = {
    constructor: jQuery,

    version: '1.0.0',

    init: function (selector) {
      // 在jquery中这里有一个复杂的判断，但是这里我做了简化
      var elem, selector;
      elem = document.querySelector(selector);
      this[0] = elem;

      // 在jquery中返回一个由所有原型属性方法组成的数组，我们这里简化，直接返回this即可
      // return jQuery.makeArray(selector, this);
      return this;
    },

    // 在原型上添加一堆方法
    toArray: function () { },
    get: function () { },
    each: function () { },
    ready: function () { },
    first: function () { },
    slice: function () { }
    // ... ...
  }

  jQuery.fn.init.prototype = jQuery.fn;

  // 实现jQuery的两种扩展方式
  jQuery.extend = jQuery.fn.extend = function (options) {

    // 在jquery源码中会根据参数不同进行很多判断，我们这里就直接走一种方式，所以就不用判断了
    var target = this;
    var copy;

    for (name in options) {
      copy = options[name];
      target[name] = copy;
    }
    return target;
  }

  // jQuery中利用上面实现的扩展机制，添加了许多方法，其中

  // 直接添加在构造函数上，被称为工具方法
  jQuery.extend({
    isFunction: function () { },
    type: function () { },
    parseHTML: function () { },
    parseJSON: function () { },
    ajax: function () { }
    // ...
  })

  // 添加到原型上，动态方法
  jQuery.fn.extend({
    queue: function () { },
    promise: function () { },
    attr: function () { },
    prop: function () { },
    addClass: function () { },
    removeClass: function () { },
    val: function () { },
    css: function () { }
    // ...
  })

  // $符号的由来，实际上它就是jQuery，一个简化的写法，在这里我们还可以替换成其他可用字符
  ROOT.jQuery = ROOT.$ = jQuery;
})(window);
```

![](https://upload-images.jianshu.io/upload_images/599584-181a154ebc9ec559.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

#### 对象封装分析

`jQuery`构造函数里声明一个fn属性，指向`jQuery.prototype`，并在原型中添加`init()`

```javascript
jQuery.fn = jQuery.prototype = {
  init: {}
}
```

`init()`作为实际的构造函数，其`prototype`指向原型`jQuery.prototype`

```javascript
jQuery.fn.init.prototype = jQuery.fn;
```

构造函数jQuery中，返回了init的实例对象

```javascript
var jQuery = function (selector) {
  // 在jQuery中直接返回new过的实例，这里的init是jQuery的真正构造函数
  return new jQuery.fn.init(selector)
}
```

最后对外暴露入口时，将字符$与jQuery对等起来

```javascript
ROOT.jQuery = ROOT.$ = jQuery;
```

**避免无节制的使用`jQuery`**,每次执行`$()`后都会重新生成新的实例，对于内存的消耗非常大。正确的做法是既然是同一个对象，那么就用一个变量保存起来后续使用即可

```javascript
var $test = $('#test');
var width = parseInt($test.css('width'));
if(width > 20) {
  $test.css('backgroundColor', 'red');
}
```

#### 关于静态方法，工具方法和实例方法

![](https://upload-images.jianshu.io/upload_images/599584-3302c8cc266fd7cb.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

#### jQuery插件封装

- 利用闭包创建自执行模块
  
  ```javascript
  (function($) {
  
  })(jQuery);
  ```

- 挂载方法到构造函数上或者原型上
  
  ```javascript
  (function ($) {
      $.fn.myPlugin = function () {
  
      };
  })(jQuery);
  ```
