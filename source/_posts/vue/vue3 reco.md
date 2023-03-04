---
title: Vue3 reco
categories:
  - [Vue]
tags: 
---

## 2022-7-15

> 开始复习一下 vue3 🤦‍♂️🤦‍♂️🤦‍♂️

- [x] vite

- [x] setup

- [x] ref

- [x] reactive

- [x] 回顾 vue2 响应式原理

- [x] vue3 响应式原理
1. vite
   
   [Vue.js (vuejs.org)](https://v3.cn.vuejs.org/)
   
   [vitejs.cn](https://vitejs.cn)
   
   vite 扬言要替代 webpack ，成为新一代前端架构工具。
   
   优势如下：
   
   - 开发环境中，无需打包，可以快速冷启动。
   
   - 轻量快速的热重载（HMR）。
   
   - 真正的按需编译，不再需要等待整个引用的编译完成。
   
   与传统构建对比：
   
   ![](https://cn.vitejs.dev/assets/bundler.37740380.png)
   
   ![](https://cn.vitejs.dev/assets/esm.3070012d.png)
   
   ```shell
   ## 创建工程
   npm init vite-app <project-name>
   ## 进入工程目录
   cd <project-name>
   ## 安装依赖
   npm install
   ## 运行
   npm run dev
   ```

2. setup
   
   vue3 最大的特点就是 composition API。
   
   也就是 组合式API。
   
   组件中所有的 数据、方法等，都在 setup 中进行配置，且需要在 setup 中进行返回。
   
   ```JavaScript
   export default {
    name: 'app',
      conponents: {...},
    setup(props, context) {
   
        return {
          ...
        }
    }
   }
   ```

3. ref
   
   将数据变成响应式数据（返回 `RefImpl` 实例）。
   
   基础数据类型、引用数据类型都可以。
   
   对于基础数据类型还是采用 `Object.definedProperty( )`实现的。
   
   其实在转换引用数据类型的时候其实在内部调用了`reactive`方法
   
   在 JS 中使用的时候需要 `.value`，但在 `template` 中不需要 `.value`。
   
   ```JavaScript
   import { ref } from 'vue'
   export default {
    name: 'app',
      conponents: {...},
    setup(props, context) {
         let a = ref(1);
          let b = ref("2");
        return {
          ...
        }
    }
   }
   ```

4. reactive
   
   引用类型的响应式数据（基础类型不要使用，会报错，返回 `proxy` 实例）
   
   内部采用`proxyreflect`实现。
   
   采用的是 ES6 中的新特性，也因此，vue3 在发行的同时，也回避了很多问题，不再兼容老版本了，所以技术也并非是越新的越好。
   
   ```JavaScript
   import { reactive } from 'vue'
   export default {
    name: 'app',
      conponents: {...},
    setup(props, context) {
         let a = reactive({
            n: 1,
              obj: {
                t: 2
            }
        });
        return {
          ...
        }
    }
   }
   ```

5. 回顾 vue2 响应式原理
   
   compiler、observer、watcher、dep。
   
   主要是通过 Object.defineProperty 中的 getter、setter 实现。
   
   每一个数据通过 observer 转变为 响应式数据且每一个数据都有对应的 dep 依赖管理器（也就是 dep 实例），用于存放 watcher 实例。
   
   compiler 进行模板编译，进行数据以及更新函数的绑定，且再次过程中创建 watcher 实例，并存放到相应的 dep 管理器中。
   
   当数据更新时，通过 setter 进行监控，并通知对应 dep 中的所有 watcher 进行调用更新函数，视图更新。

6. vue3 响应式原理
   
   主要通过 proxy、reflect 实现响应式，但是原始 object.defineProperty 也是存在的。
   
   基础类型就是通过 object.defineProperty 实现的。
   
   引用类型则是通过 proxy 实现。
   
   ```js
   var obj = new Proxy({}, {
   get: function (target, propKey, receiver) {
    console.log(`getting ${propKey}!`);
    return Reflect.get(target, propKey, receiver);
   },
   set: function (target, propKey, value, receiver) {
    console.log(`setting ${propKey}!`);
    return Reflect.set(target, propKey, value, receiver);
   },
   delete: function(target, propKey) {
      return Reflect.deleteProperty(target, propKey);
   }
   });
   ```

## 2022-7-16

> 腰背极其酸胀，tmd，真是 "麻绳专挑细处断噩运只找苦命人" 🤬🤬

- [x] setup 参数

- [x] computed
1. setup 参数（props、context）
   
   setup 运行在 `beforecreted` 之前，`this` 为 `undefined`。
   
   setup 中有两个参数，`props`、`context`。
   
   props：父组件传递的数据，是一个 proxy 实例对象。
   
   传递不接受，会有警告。
   
   接受不全，会有警告。
   
   接受采用 props 字段进行接收，和 vue2 一样
   
   ```js
    export default {
        name: '...',
          props: [...]
          setup(props) {
   
        }    
    }
   ```
   
   context：上下文对象（attrs、emit、slots）。
   
   相当于原来的 `this.$attrs、this.$emit、this.$slots`。
   
   但是由于 setup 中 this 为 undefined ，所以做了特殊的处理。
   
   在使用是均需要通过 emits、attrs、slots 字段进行接收。
   
   ```js
    // 父
    <child @fun = "xxx"/>
    export default {
        name: '',
          setup(props, context) {
              let xxx = function(xxx_data) {
                console.log(xxx_data);
            }
              return{
                xxx
            }
        }
    }
   
    // 子
    export default {
        name: '',
          emits: ['fun']
          setup(props, context) {
              let xxx_data = 1;
            context.emit('fun', xxx_data);
              return {
   
            }
        }
    }
   ```

## 2022-7-18

> “小镇做题家”🐮🐴

- [x] watch

- [x] watchEffect
1. watch
   
   composition API
   
   不同于 vue2 的是，vue3 中实现了组合式API，通过导入方法调用的方式进行使用
   
   `watch( params, callback, { } )`
   
   对于配置项，例如 `immediate`、`deep`，可以以对象的形式作为参数进行配置。
   
   对于监听多个数据，采用数组的形式进行传递。
   
   `watch( [ params1, params2,..... ], callback, { } )`
   
   对于`ref`定义的对象数据，在监视的时候需要`.value`，否则 watch 监测不到其变化，原因在于watch 在监测的时候，根据 value 的值进行监测，而此时 value 的值是proxy 对象，对于对象的变化，只有在这个对象的地址发生变化时候，watch 才能监测到，但实际上变化的是 `proxy` 内部的 `target` 的属性，所以需要`.value`，或者开启深度监视也可。
   
   ```js
   import { watch } from 'vue'
   export default {
    name: '',
      setup() {
        let a = ref(0);
          let b = ref(1);
          let obj = ref({
            t: 1
        })
        watch(a, (newValue, oldValue) => {
            console.log(newValue, oldValue);
        }, {
            immediate: true,
              deep: true
        });
   
          // 监测 ref 对象数据
          // 不采用.value，开启深度监视
          watch(obj, (newValue, oldValue) => {
            console.log(newValue, oldValue);
        },{deep: true});
   
          // 采用.value，不开启深度监视，监视内层数据
          watch(obj.value, (newValue, oldValue) => {
            console.log(newValue, oldValue);
        });
          return {
   
        }
    }
   }
   ```
   
   对于 `reactive` 数据，watch 是无法捕获到 `oldValue` 的，且是强制开启了深度监视（deep配置无效）。
   
   对于`监视 reactive 数据中的某一个属性`时，watch 第一个参数需要以函数返回值的形式呈现，监视某多个属性（reactive中的属性）也是通过数组形式传递。
   
   当监视reactive中的对象数据时（更加深层次的数据），需要配置 deep 选项
   
   ```js
   import { reactive, watch } from 'vue'
   export default {
   let a = reactive({
     t: 1,
     job: {
       k: {
       op: 2
       }
     }
   })
   // 监视 reactive 数据
   // deep 无效，强制开启
   watch(person, (newValue, oldValue) => {
     console.log(newValue, oldValue);
   })
   
   // 监视 reactive 数据中的数据（非对象）
   watch(() => person.t, (newValue, oldValue) => {
     console.log(newValue, oldValue);
   })
   
   // 监视 reactive 数据中的对象数据
   watch(() => person.job, (newValue, oldValue) => {
     console.log(newValue, oldValue);
   }, {deep: true})
   }
   ```

2. watchEffect
   
   同样也是监听，且比较智能。
   
   不同于 watch 需要指明监视的属性以及回调。
   
   有点类似于 computed，都是对于 依赖的数据 进行监听，不同的是 computed 更注重返回的结果，watchEffect 正注重过程对于数据的使用。
   
   表面上是监听全部，实际上是监听，传入回调函数中，`所使用到的数据`。
   
   ```js
   import { reactive, watchEffect } from 'vue'
   export default {
   let a = reactive({
   t: 1,
   job: {
     k: {
         op: 2
     }
   }
   })
   
   watchEffect(() => {
     let op = a.job.k.op
   });
   }
   ```

## 2022-7-20

> 新的一天从打油开始😒

- [x] vue3 生命周期
1. vue3 生命周期
   
   ![实例的生命周期](https://v3.cn.vuejs.org/images/lifecycle.svg)
   
   composition API
   
   由于 setup 执行在 created之前，所以 created 无需配置。
   
   原 vue2 那种配置项形式写也是可以用的。
   
   beforeMount => onBeforeMount
   
   mounted => onMounted
   
   beforeUpdate => onBeforeUpdate
   
   updated => onUpdated
   
   beforeUnmount => onBeforeUnmount
   
   unmounted => onUnmounted
   
   activated => onActivated
   
   deactivated => onDeactivated

## 2022-7-21

> 都鸡儿梦到公寓申请好了，呜呜呜😥

- [x] hook

- [x] toRef

- [x] toRefs
1. 自定义 hook 函数
   
   hook 函数 实际上就是对于代码的复用（composition API 的复用）。
   
   且需要进行返回。
   
   此刻，我们需要实现这样的功能：鼠标点击获取点击的位置，并且在组件销毁的时候，移除监听。
   
   ```v
   // 不使用 hook
   <template>
    <h1>{{ point.x-- - point.y }}</h1>
   </template>
   
   <script>
   import { reactive, onMounted, onBeforeUnmount } from "vue";
   export default {
    setup() {
      let point = reactive({
        x: 0,
        y: 0,
      });
   
      function savePoint(event) {
        point.x = event.pageX;
        point.y = event.pageY;
        console.log(point.x, point.y);
      }
   
      onMounted(() => {
        window.addEventListener("click", savePoint);
      });
   
      onBeforeUnmount(() => {
        window.removeEventListener("click", savePoint);
      });
   
      return {
        point,
      };
    },
   };
   </script>
   ```
   
   当其他组件也想复用该功能，显然复制粘贴代码是很捞的。
   
   此时 hook 就诞生了，其实也就是上一个版本的 mixin。
   
   规范：
+ 通常，hook 函数放在 `/src/hooks` 中

+ 每一个 hook 函数以 `usexxx.js` 方式进行命名
  
  ```js
  // /src/hooks/usePoint.js
  import { reactive, onMounted, onBeforeUnmount } from "vue";
  export function savePoint() {
  let point = reactive({
      x: 0,
      y: 0,
    });
  
  function savePoint(event) {
    point.x = event.pageX;
    point.y = event.pageY;
    console.log(point.x, point.y);
  }
  
  onMounted(() => {
    window.addEventListener("click", savePoint);
  });
  
  onBeforeUnmount(() => {
    window.removeEventListener("click", savePoint);
  });
  
  return point;
  }
  ```
  
  ```v
  // 使用 hook
  <template>
    <h1>{{ point.x-- - point.y }}</h1>
  </template>
  
  <script>
  import { reactive } from "vue";
  import { savePoint } from "./src/hooks/"
  export default {
    setup() {
      let point = 
  
      return {
        point,
      };
    },
  };
  </script>
  ```
2. toRef
   
   将响应式对象中的某个属性单独提供出去给外部使用，并且不改变原有数据以及不创建新的ref数据。
   
   不同于 ref 的是，toRef 通过 `get` 保留了原数据的引用。
   
   `toRef(target, propName)`
   
   ```js
   import { reactive, toRef } from 'vue'
   export default {
     setup() {
       let person = reactive({
         name: 'amos',
         age: 21,
         job: {
           salary: 3300  
         }
       });
   
       return {
         name: toRef(person, 'name'),
         job: toRef(person, 'job'),
       }
     }
   }
   ```

3. toRefs
   
   将对象属性中的所有属性全部提取出去提供使用。
   
   但是在 return 的时候需要通过 展开运算符（...） 进行展开。
   
   `toRefs(target)`
   
   ```js
   import { reactive, toRefs } from 'vue'
   export default {
     setup() {
       let person = reactive({
         name: 'amos',
         age: 21,
         job: {
           salary: 3300  
         }
       });
   
       return {
         ...toRefs(person)
       }
     }
   }
   ```

> okey，至此一些 常用 的 Composition API 就告一段落了😃

## 2022-7-22

> 感觉当前的状态，还是很舒服的，但是我也不知道是对是错😶‍🌫️
> 
> 你很特别，也给了我不一样的感觉。

- [x] shallowReactive

- [x] shallowRef

- [x] readonly

- [x] shallowReadonly
1. shallowReactive
   
   不同于 Reactive 的是，其只响应式第一层数据，深层次的数据不具备响应式。
   
   那么我们在对于数据我们只修改第一层数据的时候，可以进行使用。
   
   ```js
   import { shallowReactive } from 'vue'
   export default {
     setup() {
       let person = shallowReactve({
         name: 'amos',
         job: {
           salary: 3300
         }
       })
       return {
         person
       }
     }
   }
   ```

2. shallowRef
   
   不同于 Ref 的是，在于传递参数的时候：① 如果传递的是非对象，那么两者是相同的。② 如果传递的是对象，那么 shallowRef 将不会调用 Reactve 进行响应式的转换，而是以 Object 的形式返回。
   
   【 对于以上两种，可能就是 vue3 为了轻量化而设计的，也就是说，这两种方式可能更加轻量化，但是相对而言的就是，功能有所阉割。】
   
   ```js
   import { shallowRef } from 'vue'
   export default {
     setup() {
       let person = shallowRef({
         name: 'amos',
         job: {
           salary: 3300
         }
       })
       return {
         person
       }
     }
   }
   ```

3. readonly
   
   `readonly(Responsive_data)`
   
   将响应式数据变成 只读 模式，无论是对象或者是非对象，内部属性均不可改变。
   
   这也就是不同于 const 之处。
   
   const 中仅仅是表层地址不能，而内部属性均可以修改。
   
   使用场景：
   
   当数据并不是当前组件的数据，而是从别人那里传递过来的数据。
   
   这个时候就可以，对传递过来的数据进行 readonly 的加工，从而保证原始数据的不变性以及安全性。

4. shallowReadonly
   
   人如其名，浅层次的只读，对于对象类型，内部深层次的数据仍然可以改变。

## 2022-8-1

- [x] compositionAPI 的动机和目的

- [x] compositionAPI 的心智负担和optionAPI的兼容性

- [x] vue3 渲染流程

- [x] custom renderer

- [x] pixi.js
1. compositionAPI 的动机和目的
   
   改善 逻辑专注点。
   
   原本 setup 就是函数，天然的复用。
   
   也就是之前说的 hooks [2022-7-21]。

2. compositionAPI 的心智负担和optionAPI的兼容性
   
   心智负担：
   
   reactive 响应式数据丢失问题。
   
   可以采用 toRefs 解决。
   
   兼容性：
   
   没有 this。
   
   在 optionAPI 之前。
   
   在 beforeCreate 和 created 之前。

3. 渲染流程
   
   templete -> render -> vnode(element, tree) -> mountElement -> element -> append("#app")
   
   render() 函数返回 vnode 虚拟节点。
   
   web 应用是挂载到body上的，要是我们需要挂载到其他平台呢？比如说 ios、canvas...
   
   其实主要不同点在于 mountElement 这个渲染环节，在这个环节上，所创建真实节点的方式，或者说调用的 API 不同。
   
   比如 web 应用中，调用的就是 document 上的 api，createElement。
   
   因此 mountElement 其实可以拆分出多个渲染接口：
   
   createElement -> insert
   
   这些在其他平台也都有其实现的方式。
   
   vue 通过 custom renderer 自定义渲染平台。
   
   对应的 api === createRenderer()

4. custom renderer
   
   允许用户自定义目标渲染平台。
   
   动机：不局限于浏览器的 dom 平台，可以把 vue 的开发模型拓展到其他平台。
   
   实现渲染接口：
   
   - createElement
   
   - insert
   
   - setElementText
   
   - createText
   
   - parentNode
   
   - remove
   
   - patchProp 设置 props
   
   canvas 渲染接口：pixijs
   
   比如 如下自实现的 dom render。
   
   ```js
   // main.js
   const { createRenderer } = require('vue');
   import App from "./App.vue";
   
   const renderer = createRenderer({
     createElement(type){
       console.log(type)
       console.log("createElement------")
       return document.createElement(type);
     },
     setElementText(node, text){
       console.log(node);
       console.log(text);
       console.log("setElementText------")
       node.append(document.createTextNode(text))
     },
     insert(el,parent){
       console.log(el);
       console.log(parent);
       console.log("insert------")
       parent.append(el);
     },
   
   });
   
   console.log(renderer)
   renderer.createApp(App).mount(document.querySelector("#app"));
   ```
   
   ```v
   <template>
     <div>213</div>
   </template>
   
   <script>
   export default {
     name: 'App',
     components: {},
   };
   </script>
   
   <style>
   #app {
     font-family: Avenir, Helvetica, Arial, sans-serif;
     -webkit-font-smoothing: antialiased;
     -moz-osx-font-smoothing: grayscale;
     text-align: center;
     color: #2c3e50;
     margin-top: 60px;
   }
   </style>
   ```

5. pixi.js
   
   [pixi.js](pixijs.io)
   
   使用：
   
   - 初始化： Application
   
   - 创建矩形： Graphics
   
   - 创建图片： Sprite
   
   - 创建文本： Text
   
   - 创建容器： Container
   
   - 帧循环：
     
     - app.ticker.add()
     
     - app.ticker.remove()
   
   - 点击事件：
     
     - interactive = true
     
     - target.on('pointertap', () => {})
   
   ```v
   <template>
     <rect x="100" y="200">
       <circle x = "200" y = "200"></circle>
     </rect>
   </template>
   
   <script>
   export default {
     name: 'App',
     components: {},
   };
   </script>
   
   <style>
   #app {
     font-family: Avenir, Helvetica, Arial, sans-serif;
     -webkit-font-smoothing: antialiased;
     -moz-osx-font-smoothing: grayscale;
     text-align: center;
     color: #2c3e50;
     margin-top: 60px;
   }
   </style>
   
   ```
   
   ```js
   const { createRenderer } = require('vue');
   const { Application, Graphics } = require('pixi.js');
   import App from "./App.vue";
   
   const app = new Application();
   document.body.appendChild(app.view);
   
   const renderer = createRenderer({
     createElement(type){
       let element;
       if(type === "rect") {
         element = new Graphics();
         element.beginFill(0xff0000);
         element.drawRect(0, 0, 100, 100);
         element.endFill();
       } else if(type === "circle") {
         element = new Graphics();
         element.beginFill(0xffff00);
         element.drawCircle(0, 0, 100);
         element.endFill();
       }
       return element;
     },
     patchProp(el, key, preValue, nextValue) {
       el[key] = nextValue;
     },
     insert(el,parent){
       parent.addChild(el);
     },
   
   });
   
   renderer.createApp(App).mount(app.stage);
   ```

## 2022-8-8

- [x] vue 核心模块

- [x] reactivity
1. vue 核心模块
   
   ![](https://i.bmp.ovh/imgs/2022/08/08/1f60a6b52c861611.png)
   
   - @vue/runtime-dom
     
     自定义 render 层，处理渲染层的 dom。
   
   - @vue/runtime-core
     
     vue 核心运行时逻辑。
   
   - @vue/reactivity
     
     vue3 响应式库。
   
   - @vue/compiler-dom
     
     编译dom。
   
   - @vue/compiler-core
     
     编译核心。
   
   - @vue/compiler-sfc
     
     编译 sfc（单文件组件）

2. reactivity
   
   安装： `npm i @vue/reactivity`
   
   核心在于 收集依赖 以及 通知依赖。
   
   触发数据 get 的时候进行 收集依赖。
   
   触发数据 set 的时候进行 通知依赖，去执行所有的依赖，进行视图的更新。
   
   无论是 watch、computed 还是 watchEffect，底层都是对 effect 的二次封装。
   
   通过 effect 收集依赖，当数据更新的时候，effect 就会执行一次。
   
   ![](https://i.bmp.ovh/imgs/2022/08/15/6ec2374212692cd6.png)
   
   简单实现响应式：
   
   ```js
   import { ref, effect } from "@vue/reactivity";
   
   const App = {
     render(context) {
       effect(() => {
         let app = document.querySelector("#app");
         app.textContent = ``;
         const div = document.createElement("div");
         // 收集依赖
         div.textContent = context.a.value;
         app.append(el)
       });
     },
     setup() {
       let a = ref(10);
       window.a = a;
       return {
         a,
       };
     },
   };
   App.render(App.setup());
   
   // 触发依赖
   window.a = 20
   ```

3. reactivity-ref
   
   仍然是 get 收集依赖，set 通知依赖，通过 Dep 收集到 effects 中。
   
   Dep -> Ref
   
   ```javascript
   // reactivity.js
   export class Dep {
     constructor(value) {
       this._val = value;
       this.effects = new Set();
     }
   
     get value() {
       this.depend();
       return this._val;
     }
   
     set value(val) {
       this._val = val;
       notice();
     }
   
     depend() {
       if (currentEffect) {
         this.effects.add(currentEffect);
       }
     }
   
     notice() {
       this.effects.forEach((effect) => {
         effect();
       });
     }
   }
   
   let currentEffect = null;
   
   export function effectWatch(fn) {
     currentEffect = fn;
     fn();
     currentEffect = null;
   }
   
   let a = new Dep(10);
   
   effectWatch(() => {
     let b = a.value;
     console.log(b);
   });
   
   a.value = 20;
   
   
   ```
