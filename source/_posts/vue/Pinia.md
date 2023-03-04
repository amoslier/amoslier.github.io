---
title: Pinia 基础
categories:
  - [Vue]
tags: 
---

## Pinia

### 为什么你应该使用 Pinia？

- Devtools 支持
  - 追踪 actions、mutations 的时间线
  - 在组件中展示它们所用到的 Store
  - 让调试更容易的 Time travel
- 热更新
  - 不必重载页面即可修改 Store
  - 开发时可保持当前的 State
- 插件：可通过插件扩展 Pinia 功能
- 为 JS 开发者提供适当的 TypeScript 支持以及**自动补全**功能。
- 支持服务端渲染

### 核心概念

#### Store

- Option Store
  
  - store => data
  
  - getter => computed
  
  - actions => methods

为方便上手使用，Option Store 应尽可能直观简单。

```js
export const useCounterStore = defineStore('counter', {
  state: () => ({ count: 0 }),
  getters: {
    double: (state) => state.count * 2,
  },
  actions: {
    increment() {
      this.count++
    },
  },
})
```

- Setup Store
  
  - ref() => state
  
  - computed() => getters
  
  - function() => actions
  
  Setup 具有更多的灵活性，但使用组合式函数也会让 SSR 变得复杂。

```js
export const useCounterStore = defineStore('counter', () => {
  const count = ref(0)
  function increment() {
    count.value++
  }

  return { count, increment }
})
```

#### State

##### init

```js
import { defineStore } from 'pinia'

const useStore = defineStore('storeId', {
  // 为了完整类型推理，推荐使用箭头函数
  state: () => {
    return {
      // 所有这些属性都将自动推断出它们的类型
      count: 0,
      name: 'Eduardo',
      isAdmin: true,
    }
  },
})
```

##### use

```js
const store = useStore()
store.count++
```

##### reset

```js
const store = useStore()
store.$reset()
```

##### $patch

```js
cartStore.$patch((state) => {
  state.items.push({ name: 'shoes', quantity: 1 })
  state.hasChanged = true
})
```

##### $subscribe

```js
export default {
  setup() {
    const someStore = useSomeStore()
    // 在组件被卸载后，该订阅依旧会被保留。
    someStore.$subscribe(callback, { detached: true })
    // ...
  },
}
```
