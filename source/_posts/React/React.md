---
title: React 基础
categories:
  - [React]
tags: 
---

# 生命周期：

![](C:\Users\lenovo\AppData\Roaming\marktext\images\2022-09-02-14-11-20-image.png)

## 重要的三个钩子

### render

执行 1+n 次。

每一次 setState 都会 render。

无论是挂载还是更新阶段，在`render`之前的生命周期函数都不会更新`this.state`和`props`，直到`render`执行完成后，数据才会更新。

render 之后是不能够再操作数据的，否则将进入死循环。

### componentDidMount

### componentWillUnmount

`ReactDOM.unmountComponentAtNode()`

## Mounting

- `constructor()`
- `componentWillMount()`
- `render()`
- `componentDidMount()`

## Updating

- `componentWillReceiveProps()`
- `shouldComponentUpdate()`
- `componentWillUpdate()`
- `render()`
- `componentDidUpdate()`

## Unmounting

- `componentWillUnmount()`

# 数据驱动

单向数据流

state -> setState -> render -> view

# 实例核心属性

## state

state -> setState()

setState 是异步的。

setState 是合并不是替换。可以通过定时器摆脱 react 的控制。

setState 若要依赖当前数据，可以通过函数式进行操作。

- setState((state) => {})

setState 生命周期：

- getDerivedStateFromProps

- shouldComponentUpdate，根据返回值判断是否要继续更新。

- render，执行真正的更新。

- getSnapshotBeforeUpdate

- componentDidUpdate

## props

function(props){}

class cc extends React.Component {

  this.props;

  constructor(props){

    this.props = props;

  }

}

## refs

### 字符串形式

class Demo extends React.Component {

  const {input} = this.refs;

  render() {

    return (

      <>

        \<input ref={input}>

      </>

    )

  }

}

### 回调形式

#### 内联形式

内联形式，ref 回调回执行两次，第一次传递 null 第二次传递 DOM

\<input ref={(c) => {this.input = c}>

#### 非内联形式

class demo extends React.Component {

  handle(c){

    this.input = c;

  }

  return (

    <>

      \<input ref={this.handle}>

    </>

  )

}

> 如果 `ref` 回调函数是以内联函数的方式定义的，在更新过程中它会被执行两次，第一次传入参数 `null`，然后第二次会传入参数 DOM 元素。这是因为在每次渲染时会创建一个新的函数实例，所以 React 清空旧的 `ref` 并且设置新的。通过将 `ref` 的回调函数定义成 `class` 的绑定函数的方式可以避免上述问题，但是大多数情况下它是无关紧要的。

### createRef

class demo extends React.Component {

  input = React.createRef();

  return (

    <>

      \<input ref={this.input}>

    </>

  )

}

# Hooks

React.FC 适用

### useState

const [color, setColor] = useState("")

触发 render

异步

useState -> render -> Effect

### useEffect

副作用。

useEffect(() => {},[propsName])

监听 数据变化之后 进行执行回调函数。

不写参数，在每次 render 的时候执行回调。

render之后执行。

React采用异步调用的方式处理Effect，等主线程完成、DOM更新、js执行完成、试图绘制完成，才执行，所以effect回调函数不回阻塞浏览器绘制视图。

### useLayoutEffect

DOM更新之后，浏览器绘制视图之前，且比Effect执行更早。

避免在Effect中操作DOM时导致二次绘制，出现闪现现象。

其回调执行会阻塞浏览器绘制。

### useInsertionEffect

DOM更新之前，且比LayoutEffect执行更早。

解决 CSS-in-JS 在渲染中注入样式的性能问题。

### useContext

1. 接收上下文。
- create

const MyContext = React.createContext(defaultValue);

import {createContext} from "react"

const MyContext = createContext(defaultValue);

- Provider

\<MyContext.Provider value={}>

- use

const value = this.context;

const value = useContext(MyContext);

\<MyContext.Consumer>
 {value => { }}
</MyContext.Consumer>

### useRef

1. 获取元素、缓存状态。

const count = useRef(0)

count.current = count.current++;

2. 不触发 render

connect DOM

const inputRef = useRef(null);

\<input ref={inputRef}/>

inputRef.current.focus();

# Router

## 安装

`yarn add react-router-dom`

## 基本使用

\<Route exact/> exact 严格匹配模式，可能导致二级路由无法匹配

\<Link />

\<BrowserRouter></BrowserRouter>

\<NavLink></NavLink> 高亮

\<Switch></Switch> 高效路由匹配

\<Redirect to="/about" /> 所有都无法匹配时重定向

```jsx
import React, { Component } from 'react'
import { Link, Route } from 'react-router-dom'
import Home from './components/Home'
import About from './components/About'

export default class App extends Component {
  render() {
    return (
      <div>
        <div className="list-group">
          <Link className="list-group-item" to="/about">
            About
          </Link>
          <Link className="list-group-item" to="/home">
            Home
          </Link>
        </div>
        <div className="panel-body">
          <Route path="/about" component={About} />
          <Route path="/home" component={Home} />
        </div>
      </div>
    )
  }
}
```

```tex
history:
  go: ƒ go(n)
  goBack: ƒ goBack()
  goForward: ƒ goForward()
  push: ƒ push(path, state)
  replace: ƒ replace(path, state)

location:
  pathname: "/home/message/detail/2/hello"
  search: ""
  state: undefined

match:
  params: {}
  path: "/home/message/detail/:id/:title"
  url: "/home/message/detail/2/hello"
```

## 多级路径刷新页面样式丢失

相对路径会对照当前路由地址，因而会丢失样式。

- 取消使用相对路径，使用 `/` 或者 `%PUBLIC_URL%`

- 使用 HashRouter

## 嵌套路由

子路由需要加上父路由的`path`

## 参数传递

- params
  
  `/home/message/detail/:age/:name`
  
  `/home/message/detail/21/amos`
  
  `useParams() -> {age: '21', name: 'amos'}`
  
  需要配置

- search
  
  `/home/message/detail?name="11"`
  
  `useQuery().get('name')`

- state
  
  `useHistory().push('/user/role/detail', { id: item });`
  
  `useLocation().state;`

## Hooks

### useHistory

- push

- replace

- go

- goForward

- goBack

### useLocation

```ts
declare function useLocation(): Location;

interface Location extends Path {
  state: unknown;
  key: Key;
}
```

- state

- key

### useParams

```ts
declare function useParams<K extends string = string>(): Readonly<Params<K>>;
```

- {}

### useQuery

- get

# Redux

![redux 工作流程图](https://brucecai55520.gitee.io/my-notes/React/images/redux.png)

**reducer -> createStore -> store -> getState、dispath、subscribe**

## createStore

- `createStore(reducer: (preState:any; action:{type: 'string'; data: any})=>{})`

## Store

- `getState()`

- `dispath(action: {type: string; data: any;})`

- `subscribe(func: function)`
