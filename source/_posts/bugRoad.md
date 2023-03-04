---
title: Bug Road
categories:
  - [BugRoad]
tags: 
---

# Bug Road

## Vue

---

#### input @blur & @click 冲突

🤔发现问题

  input 失去焦点 和 其他点击 事件冲突的时候，会导致一些视图逻辑问题。

🙅‍♂️拒绝妥协，坚持解决

  给 @blur 加个 setTimeout 让其延后执行即可。

### Vue3

#### reactive对象再次赋值，失去响应式

🤔发现问题

  `const Akira = reactive({ a:1 });`

  `Akira = { a:2 };`

  后续响应式丢失。

🙅‍♂️拒绝妥协，坚持解决

响应式丢失，主要是因为引用数据存储，是按照引用地址存储的，直接赋值会导致，地址发生变化，从而响应式丢失。

- 改用 ref

- 在外层在套一层
  
  `const state = reactive({ Akira: { a:1 } });`

#### VueRouter 传递 state 参数无效

🤔发现问题

`Error with push/replace State DOMException: Failed to execute 'pushState' on 'History': <Object> could not be cloned.`

🙅‍♂️拒绝妥协，坚持解决

这里的报错原因，可能是因为传递的 state 参数对象类型出现了问题。在 Vue3 中数据大多为 proxy 对象，需要通过 toRaw 将其转换为普通对象类型。

### vuex

#### vuex数据持久

🤔发现问题

  vuex 中的数据，在刷新的时候数据会丢失。

🙅‍♂️拒绝妥协，坚持解决

- [vuex-persistedstate](https://www.npmjs.com/package/vuex-persistedstate)
- [vuex-persist](https://www.npmjs.com/package/vuex-persist)
- localStorage

## NPM & yarn

---

### 常用命令

| Command                        | Description              |
| :----------------------------- | :----------------------- |
| `npm view packageName version` | 查看包的可安装版本       |
| `npm init -y`                  | yarn init -y             |
| `npm i xxx -d `                | yarn add                 |
| `npm i xxx -s`                 | yarn                     |
| `npm i xxx -g`                 | 全局安装依赖             |
| `npm i xxx --force`            | 忽略上游冲突，覆盖依赖   |
| `npm i xxx --legacy-peer-deps` | 忽略上游冲突，不覆盖依赖 |
| `npm config get prefix`        | 查看全局安装路径         |

### 快速清除 node_modules

🤔发现问题

  通常采用直接删除 node_modules 是十分缓慢。

🙅‍♂️拒绝妥协，坚持解决

  `npm install rimraf -g`

  `rimraf node_modules`

### peerDependencies 依赖冲突

🤔发现问题

  `npm install` 爆红一片，大多是上流依赖冲突了。

🙅‍♂️拒绝妥协，坚持解决

  我们可以通过一些指令使得绕过该冲突。

  `npm i --force` 暴力安装覆盖之前安装的依赖。

  `npm i --legacy-peer-deps` 跳过对等依赖。

> In the new version of npm (v7), by default, `npm install` will fail when it encounters conflicting *peerDependencies*. It was not like that before.
> 
> Take a look [here](https://github.blog/2021-02-02-npm-7-is-now-generally-available/#peer-dependencies) for more info about peer dependencies in npm v7.
> 
> The differences between the two are below -
> 
> - `--legacy-peer-deps`: ignore all *peerDependencies* when installing, in the style of npm version 4 through version 6.
> 
> - `--strict-peer-deps`: fail and abort the install process for any conflicting *peerDependencies* when encountered. By default, npm will only crash for *peerDependencies* conflicts caused by the direct dependencies of the root project.
> 
> - `--force`: will force npm to fetch remote resources even if a local copy exists on disk.

### node_modules 冗余拷贝

🤔发现问题

  时长我们再安装依赖的时候出出现一下情况：

```shell
root
└── node_modules
 ├── A@2.0.0
 ├── B@1.0.0
 │   └── node_modules
 │       └── C@2.0.0
 ├── C@2.0.0
 ├── D@0.6.5
 ├── E@1.0.3
 └── F@1.0.0
     └── node_modules
         └── C@2.0.0
```

  此时 `c@2.0.0` 也就是冗余的。

🙅‍♂️拒绝妥协，坚持解决

  好在 npm 提供了单独的命令，去去除这种冗余拷贝。

  `npm dedupe`

  顺便提一句：yarn 在安装依赖的时候会自动执行 dedupe 操作。

## webpack

---

### 自定义 Plugin 报错 打包终止

🤔发现问题

  自定义 Plugin 报错。

(node:9776) [DEP_WEBPACK_COMPILATION_ASSETS] DeprecationWarning: Compilation.assets will be frozen in future, all modifications are deprecated.
BREAKING CHANGE: No more changes should happen to Compilation.assets after sealing the Compilation.
        Do changes to assets earlier, e. g. in Compilation.hooks.processAssets.
        Make sure to select an appropriate stage from Compilation.PROCESS_ASSETS_STAGE_*.
(Use `node --trace-deprecation ...` to show where the warning was created)

🙅‍♂️拒绝妥协，坚持解决

  意思是： Compilation.assets will be frozen in future  就是 Compilation.assets 将来的`webpack`的版本中会被冻结，建议在 compilation 的 seal 阶段去处理资源。（也就是说可以在，资源压缩之后，冻结之前，对资源进行处理，但是compilation可能会触发多次）

`compiler.hooks.emit.tapAsync("pluginName", (compilation) => {})`

  打包终止原因是 异步钩子，需要调用回调，才会继续往下执行

- 改用 `tap` 定义钩子
- 或者添加 `callback`，并调用

  之后就可以打包成功了，但可能打包之后仍然会有警告，这里可以暂时忽略

### 自定义 Loader 通过 file-loader 自定义打包图片字体等资源 路径配置

🤔发现问题

  直接写文件名称，会输出到root目录。

🙅‍♂️拒绝妥协，坚持解决

  `filename`加上路径会自动匹配，并生成目录以及`filename`

```js
const loaderUtils = require("loader-utils");

module.exports = function (content) {
  // filename
  const filename = loaderUtils.interpolateName(this, "images/[hash:8].[ext]", {
    content
  })

  // output file
  this.emitFile(filename, content);

  // export
  return `export default '${filename}'`;
}

module.exports.raw = true;
```

### 图片压缩 gifsicle 缺失 autoreconf

🤔发现问题

  npm 安装 gifsicle 报错。

  ERROR gifsicle pre-build test failed

🙅‍♂️拒绝妥协，坚持解决

  安装过程 js 编译，找不到 autoreconf，安装终止

- `npm install --ignore-scripts`
- 或者将 gifsicle 版本降到 4+ 一下 

### npm i css-loader style-loader -d 报错

🤔发现问题

  npm 安装 css-loader style-loader 报错 。

  ERROR：unable to resolve dependency tree

🙅‍♂️拒绝妥协，坚持解决

  版本不匹配，css-loader、style-loader版本过高

  npm i css-loader@3 style-loader@1 -d

### mini-css-extract-plugin 抽离css

🤔发现问题

  webpack 抽离 css 报错。

  ERROR in ./src/css/main.css Module build failed (from ./node_modules/mini-css-extract-plugin/dist/

🙅‍♂️拒绝妥协，坚持解决

  style-loader：使用`<style>`将css-loader内部样式注入到我们的HTML页面

  mini-css-extract-plugin是将css抽离出来

  删除style-loader，可以正常打包

### sourceMap与optimization_CSS压缩冲突

🤔发现问题

  sourceMap与optimization_CSS压缩冲突导致.map文件生成失败。

🙅‍♂️拒绝妥协，坚持解决

  plugins 中采用 new **CssMinimizerPlugin**() 压缩CSS

### ESlint import & export 只能在顶部

🤔发现问题

  ESLint 配置后，规定 import 和 export 的书写位置问题。

  ERROR Parsing error: 'import' and 'export' may only appear at the top level

🙅‍♂️拒绝妥协，坚持解决

  `npm i eslint-plugin-import -d`

  添加配置     plugins: ["import"]

## Javascript

---

### map 转换成对象数组

🤔发现问题

`map => key-value`

通常使用到的 `Array.from` 转换，会得到`[[key, value],....]`

🙅‍♂️拒绝妥协，坚持解决

> `Array.from` 有一个回调函数，可以进一步把控数据的流向以及类型。

`Array.from(map, ([key, value]) => ({ key, value }));`

### nodeJS 打包

🙇‍♂️Flow

  pkg.js

1. 全局安装
   
   `npm i pkg -g`

2. 配置 package.json
   
   (1) bin 指定启动文件
   
   (2) script 配置命令
   
   (3) `pkg .` 寻找指定目录下的`package.json`文件，然后在找`bin`字段作为入口文件
   
   (4) `-t` 指定打包平台
   
   (5) `-o` 指定输出文件名
   
   ```json
   {
   ...,
   "bin": "./app.js",
   "scripts": {
     "test": "echo \"Error: no test specified\" && exit 1",
     "build": "pkg . -t node12-win-x64 -o server -d"
   },
   ...
   }
   ```

3. 打包后路径区别
   
   ![](https://img-blog.csdnimg.cn/20201204144110125.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1h1SGFuZzY2Ng==,size_16,color_FFFFFF,t_70)

4. node环境下载慢
   
   pkg 打包需要特殊的 node 环境，会去 node pkg 缓存中去找，找不到就去 github 下载。
   
   每次打包的时候会显示下载的版本，可以去 github 先下载好，然后放到缓存去，缓存大致位置在：`C:\Users\lenovo\.pkg-cache`，可以用 `everything` 搜 `.pkg-cache`。
   
   [pkg资源下载](https://github.com/vercel/pkg-fetch/releases)
   
   下载版本的名字可能会有不同，下载的是 `node-v8.17.0-win-x64`，但是需要的可能是`fetch-v8.17.0-win-x64`,修改一下名字即可。

## Typescript

---

### 数组 push 报错

🤔发现问题

`[ts] Argument of type 'string' is not assignable to parameter of type 'never'.`

🙅‍♂️拒绝妥协，坚持解决

数组类型未定义，会默认定义为 `never[]`。

`const arr: string[] = []; // is okey`

## CSS

---

### 多个 transform 动效抖动

🤔发现问题

```css
.target {
  &:hover {
    .target2::before {
      background-color: rgba(221, 221, 221, 0.315);
      box-shadow: 5px 5px 5px rgba(0, 0, 0, 0.3);
    }
    .target2 {
      transform: perspective(500px) rotateY(20deg);
    }
  }	
  .target2 {
    transform: all .5s
  }
  .target2:: before {
    transform: all .5s
  }
}
```

🙅‍♂️拒绝妥协，坚持解决

> 要注意动效的方向以及完成时间。同时完成，方向不一致可能会导致页面抖动的问题。

这里阴影的动效是很差的，他是一层一层去直接加载上去的，并没有一个渐变的过程。所以这里会出现抖动的情况。

我们把阴影的时间调短即可，让其提前完成。

### opacity继承

🤔发现问题

  父层背景开启透明度，导致后面文字也继承了透明度。

🙅‍♂️拒绝妥协，坚持解决

  取消这种对于父层背景透明度的设置，直接重开一个 div 做透明度解决此类问题。

### 滚动条样式

🤔发现问题

  浏览器默认的滚动条真是很不尽人意，如何change呢。

🙅‍♂️拒绝妥协，坚持解决

```css
::-webkit-scrollbar  
{  
 width: 5px;  
}

/*定义滚动条轨道 内阴影+圆角*/  
::-webkit-scrollbar-track  
{  
 border-radius: 10px;  
 background-color: rgba(0,0,0,0.1); 
}

/*定义滑块 内阴影+圆角*/  
::-webkit-scrollbar-thumb  
{  
 border-radius: 10px;  
 -webkit-box-shadow: inset 0 0 6px rgba(0,0,0,.3);  
 background-color: rgba(0,0,0,0.1);
}
```

## NodeJS

### git

#### git出现文件夹后面跟@+数字

🤔发现问题

  git 上传后，文件夹后面有@+数字，且不能点击。

🙅‍♂️拒绝妥协，坚持解决

  出现子模块

  删除原来的子文件夹的.git

  删除本地git缓存

  重新add，push

  `git rm -r --cached .`

### express

---

#### 状态码

| code | description  |
|:----:|:------------:|
| 500  | Server error |
| 422  | Cilent error |
| 200  | success      |
| 304  | no modify    |

#### express + mongoDB 接口

🙇‍♂️Flow

- app.js -> 配置插件

- model -> 连接数据库 -> 创建数据模型Schema -> Model

- router -> 配置路由 -> controller -> 控制层，逻辑实现

#### 解析表单请求体

🤔发现问题

  获取 req.params 属性为 `undefined / {}`

🙅‍♂️拒绝妥协，坚持解决

express 中需要对其做特殊的配置。

- x-www-form-urlencoded
  
  `app.use(express.urlencoded())`

- application/json
  
  `app.use(express.json())`

- form-data
  
  `yarn add connect-multiparty`
  
  ```js
  const multipartyMid = multipart();
  const router = express.Router();
  
  router.post("/register", multipartyMid, userCtrl.register);
  ```

#### 本地接口跑不通

🤔发现问题

  ERROR: getaddrinfo ENOTFOUND loaclhost 

🙅‍♂️拒绝妥协，坚持解决

  localhost -> 127.0.0.1

#### express-jwt 运行报错

🤔发现问题

  TypeError: expressJWT is not a function

🙅‍♂️拒绝妥协，坚持解决

  版本问题，7.xx.xx 以上导包改了，降低版本即可

  要记得 option 中需要 `algorithms`

```js
app.use(
  expressJWT({
    secret: secretKey,
    algorithms: ["HS256"],
  }).unless({
    path: ["/user/login", "/user/register"],
  })
);
```

#### get 请求 query 传递数组

🤔发现问题

   `[get] http://localhost/user?arr=[1,2,3]` -> [404 Bad Request]

🙅‍♂️拒绝妥协，坚持解决

`[]`在URL 中属于功能性字符，需要采用`decodeURIComponent()`进行转义，或者采用一下方式传递。

- `[get] http://localhost/user?arr=1&arr=2`

- `[get] http://localhost/user?arr[0]=1&arr[1]=2`

- `[get] http://localhost/user?arr[]=1&arr[]=2`

- `[get] http://localhost/user?arr=1,2`



---

[随机ACG壁纸 @toubiec](https://acg.toubiec.cn/random.php)

[Bing 每日壁纸 @paugram](https://api.paugram.com/binghttps://api.paugram.com/bing)
