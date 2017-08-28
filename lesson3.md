---
title: 学习笔记lesson3
date: 2017-08-24 00:12:37
tags: [notebook,study]
toc: true
---

# 答疑课

* express egg koa 新手应该学那个？

  1. connect:  es5语法  没有用generator和async function
  2. koa: node 8.0开始有async function，Node 6.0 有generator，koa是connect的进化版
  3. 正式项目中用egg，对应的配套功能较多。例如deBugger能力
  4. express 新项目不推荐使用，旧项目使用较多

* void apply(plugins: Plugin...) ?

例如[TypeScript](https://github.com/Microsoft/TypeScript/blob/master/doc/spec.md)，是javascript有类型

```
function mul(a: number, b: number) {  
    return a * b;  
}
```
a,b对应number,返回number

回到我们的例子

  * `void`： 无返回值
  * `apply` 函数的名字
  * `plugins`: 参数 后面的省略号表示能传多个参数例如apply(a, b, c, d)

* callback: (err?: Error) -> void的意思 ?

callback 对应的声明如下

```
function (err?: Error) {  // 函数有可能接收到error, ?:表示有或者没有，err表示 new Error的实例
  return void  // 返回为空，函数只是用来处理error
}
```

*  `(this.events[event] = this.events[event] || []).push(fn)`的意思 ?

可以分解为如下

```js
this.events[event] = this.events[event] || [];
this.events[event].push(fn)
```


* 如何看各式各样的库源码，经验分享 ？

  1. 先看官网文档api，了解一些方法
  2. 再看测试用例
  3. 从入口文件看看源代码,按照依赖项从头开始看 
  4. 从而了解每个方法做什么事情  
  5. 写一篇文章做源代码分析总结 
  6. 再对比别人的源分析

* 老师用什么软件 ？

  * vscode(老师自用)
  
  * 推荐其他IDE
  
  * fiddler(window)
  
  * charles(Mac)
  
  * iTerm(Mac)终端
  
  * MWeb(做笔记的软件)
  
  * Phototshop、Sketch(作图)
 
  * GIT or Svn (版本控制)
 
* TypeScript适合新手学习吗 ？ 

不适合，适合有类型语言的同学学习（java、c++、c#）
大型项目中
可以使用[jsdoc](http://www.css88.com/doc/jsdoc/),对函数的类型进行注释，
也可以使用TypeScript在语言层面解决类型问题

* 还有err?: Error是ES6的写法吗？ 

这个不是es6，有类型的es4（已废弃），之所以废弃es4是因为改变巨大不兼容以前的写法

* `？：`是什么

表示有或者没有
`Swift`的基础语法，有机会可以了解

# 上次作业

ajax、jsonp、跨域请求可以通过不同的库处理，然后通过这个做分发

`Object.assign`约定义`$.extend` 把后面对象的方法`extend`到前面的对象中

```js
 const opts = this.applyPluginsWaterfall('options', Object.assign({}, this.options, options))
```

这里可以使用`this.applyPlugin`但是在实际中`opts`有可能不等于 `Object.assign({}, this.options, options)`，可能会清除些无用的参数，

`applyPlugin`是调用所有的注册在事件在`options`，没有返回值
`applyPluginsWaterfall`是串行调用注册在事件`options`上的处理函数，先入先出,后续的处理函数传入前一个处理函数的返回值和参数，函数最终返回一个处理函数的返回结果

而`Object.assign({}, this.options, options)`的好处可以不污染以前的`options`，防止一些奇怪的事情发生

# stream

[showcase2](https://github.com/mayufo/showcase2)

使用场景： 假设数据量很大，只能一块一块读取

一个数据需要10s读取，而`Nodejs`是单进程,`Promise`只会返回一次，不符合大数据的需求，这时候需要使用`stream`

 * Readable Stream 可读的流
 * Writable Stream 可写的流
 * Duplex Stream 可读可写的流
 * Transform Stream 操作被写入数据然后读出结果的流

## Readable Steam

每100毫秒读取一个字母，并且把这段流传递给`process.stdout`， `process.stdout`也是流，接收到后就打印出来

```js
/**
 * Proimse 只能回调一次，但是如果处理数据很大，需要分很多片怎么办呢？
 */
const Readable = require('stream').Readable
const rs = new Readable // 创建可读流的实例

/**
 * 创建一个方法可以中断线程
 */
function wait(time) {  // 等待time毫秒返回一个Promise
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve()
    }, time)
  })
}

/**
 * 假设我们的数据源数据很大，读取的时候只能够一块一块读取，否则会卡住很久
 */
const source = 'aiopshdviaowefipoajsdpoghapeisfhlasdfasdf'.split('')

rs._read = function () {
  !!source.length &&
    wait(100)
      .then(() => {
        if (source.length > 1) {
          rs.push(source.shift()) // 推入到可读的流里面去
        } else {
          rs.push(source.shift()) // 结束
          rs.push('\n')
          rs.push(null)
        }
      })
}

/**
 * rs | process.stdout
 * std = standard output
 * pipe 就是管道，模拟Unix
 */
rs.pipe(process.stdout)  

/**
 * a.pipe(b).pipe(c).pipe(d) =
 * a.pipe(b); b.pipe(c); c.pipe(d) ≈
 * a | b | c | d
 */
```


## Writable Stream 

创建`message.txt`文件，并且写入beep,1秒后再写入boop

```js
const fs = require('fs')
const ws = fs.createWriteStream('./message.txt')

/**
 * 向 Writable Steam 传入一段数据
 */
ws.write('beep ')

setTimeout(() => {
  /**
   * 告诉 Writeable Stream 结束了
   */
  ws.end('boop\n')
}, 1000)

```
写的流可以通过`write`来写一些东西进去

## 实际使用


### http

post一个数据来判断数据的类型

```js
// node 文档有http协议
const http = require('http')

// request: Request扩展来自Stream， response: Response扩展来自Stream
const server = http.createServer((req, res) => {  
// request body  
    let body = ''

  req.setEncoding('utf8')
  /**
   * 当有数据的时候
   */
  req.on('data', chunk => {
    body += chunk
  })

  req.on('end', () => {
    /**
     * 试试是不是 JSON 格式
     */
    try {
      const data = JSON.parse(body)
      res.write(`${typeof data}\n`)
      res.end()
    } catch(e) {
      res.statusCode = 404
      res.end(`error: ${e.message}\n`)
    }
  })
})

server.listen(8080, () => {
  console.log('listen 8080')
})

/**
 * curl localhost:8080 -d "{}"
 * curl localhost:8080 -d "\"foo\""
 * curl localhost:8080 -d "not json"
 */
```

`curl`是一个利用URL规则在命令行下工作的文件传输工具
`-d` HTTP POST方式传送数据  

通过实例了解到

* 每拿到一点东西就会做处理而不是等到整个拿到再做处理
* 情景可以想象图片的懒加载，开始图片模糊，然后逐渐清晰
* 一段一段处理，因为网络本身就是一段一段，而不是后台给的一段一段

### read-n-write

读取 `message.txt`写入 `message.clone.txt`

```js
const fs = require('fs')

/**
 * 创建一个 Readable Stream，读取 message.txt
 */
const read = fs.createReadStream('./message.txt')
/**
 * 创建一个 Writable Stream，写入 message.clone.txt
 */
const write = fs.createWriteStream('./message.clone.txt')

/**
 * 接水管
 */
read.pipe(write)
```

### gzip

对message.text做gzip压缩，这里是个Transform Stream可读可写,可以读inp,又可以写out
```js
const zlib = require('zlib')
const gzip = zlib.createGzip()
const fs = require('fs')
const inp = fs.createReadStream('message.txt')
const out = fs.createWriteStream('message.txt.gz')

/**
 * 跟前一个例子很像，只是加了一个 Transform Steam
 */
inp.pipe(gzip).pipe(out)
```


### stream总结

`stream`可以想象成管子，管子一个个接起来，里面流动的东西可以根据每个`stream`定义的方式来处理，一个个处理

`Stream` 对象都是 `EventEmitter`的实例。常用的事件有
* `data`表示当有数据可读时触发
* `end`没有更多的数据可读时触发
* `error`在接收和写入过程中发生错误时触发
* `finish`所有数据已被写入到底层系统时触发

这个数据流开始有`on`事件，结束有`end`事件

与Promise比较Stream是通过pipe来传输，一块一块处理数据，而不是整个处理
我的疑问

### generator

虽然我们可以通过stream解决一块一块处理数据，但是我们梦想的代码方式如下

```js
function XXX() {
    doSomething()
    wait(1000)
    doOther()
}
```
在es6中generator生成器

```js
function* gen() {
  yield 1
  yield 2
  yield 3
}

let g = gen()

console.log(g.next()) // {value: 1, done: false}
console.log(g.next()) // {value: 2, done: false}
console.log(g.next()) // {value: 3, done: false}
console.log(g.next()) // {value: undefined, done: true}
```

我们可以通过`generator`在每个`next()`之间增加方法来实现异步的操作

### generator-async

```js
/**
 * 做个等待函数
 */
function wait(time) {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve()
    }, time)
  })
}

/**
 * 做一个简单的运行器
 */
function runer(g) {
  const item = g.next()

  if (!item.done) {
    // 如果是 Promise 
    if (item.value instanceof Promise) {
      item.value.then((res) => {
        runer(g)
      })
    }
  }
}

/**
 * 实际代码
 */
function* gen() {
  console.log(1)
  yield wait(1000)
  console.log(2)
  yield wait(1000)
  console.log(3)
}

runer(gen())

/**
 * 业界的确实现了类似 co 之类的优秀框架，但是大型应用的错误堆栈不太友好，
 * 比较难定位，所以现在都建议直接使用 async function 原生实现
 */
```


### async-function

这里建议先安装[nvm](https://github.com/creationix/nvm),方便切换node的版本

在node 6的版本里面不支持`async-function`,只有最新的8版本支持

```js
function wait(time) {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve() // resolve(1)
    }, time)
  })
}

async function app() {
  console.log(1)
  await wait(1000)  // 类似于上个例子的 yield wait(1000) 
  console.log(2)
  await wait(1000) // 这里值可以返回promise的1值 const res = await wait(1000)
  console.log(3)
}

app()
```
与上一个最大的区别是 不需要运行器

### 推荐学习

对流的理解 [stream-handbook](https://github.com/substack/stream-handbook)

常用的是`http.createServer()`发起请求和`http.createServer()`创建一个服务器
`zlib`通常做一些gzip压处理

以前再做文件构建的时候需要`gulp`,运用到流的东西需要了解`Stream`,而`Steam`是`NodeJS`的核心编程模型

[Generator 函数的含义与用法](http://www.ruanyifeng.com/blog/2015/04/generator.html)

[Async Function](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/async_function) 

在前端中使用管道模型 [RxJS](https://buctwbzs.gitbooks.io/rxjs/content/) 比较前言和冷门，可以了解一点


通常注册事件监听

```
var button = document.querySelector('button')
button.addEventListener('click', () => console.log('Clicked!'))
```
Rxjx创建一个观察对象

```
var button = document.querySelector('button')
Rx.Observable.fromEvent(button, 'click').subscribe(() => console.log('Clicked'))
```
[RxJS可视化的例子]（https://rxviz.com/examples/basic-interval）

Basic interval 每隔1秒会take一下从take1到take4
```
Rx.Observable
  .interval(1000)
  .take(4)
```

Mouse move

当鼠标移动的时候，每300毫秒抓取一次鼠标X轴坐标
```
Rx.Observable
  .fromEvent(document, 'mousemove')
  .map(event => event.clientX) 
  .throttleTime(300)  // 300毫秒的限流
```

Input element
过滤出输入的值不为空的
```
const input = document.createElement('input');

input.setAttribute('placeholder', 'Type something');

output.prepend(input);

input.focus();

Rx.Observable
  .fromEvent(input, 'keydown')
  .map(e => e.key)
  .filter(key => key !== ' ');

```

线操作像`stream`本身就能屏蔽掉异步和非异步的操作，能够用同样的方式去写代码

* 如何理解点操作屏蔽掉异步和非异步的操作 ？
```
async1() // run task 1
 .then(async2) // run task 2
 .then(async3)  // run task 3
```
类型上面的代码，你可能无法看出是同步操作还是异步操作，像同步操作但是可以完成异步的操作，可以用同样的方式去写，因此屏蔽掉了同步和异步

* node的方法返回都不是promise,在于async结合使用的时候，都把这些方法封装一下吗 ？

  可以用`blueBird`对node方法的返回进行Promise的封装，再和async结合用

* Promise如何实现 ？

  * [promise](https://github.com/then/promise)
  * [asThread](https://github.com/miniflycn/asThread)
  * [一种模仿线程的Javascript异步模型设计&实现](http://www.cnblogs.com/justany/archive/2013/01/25/2874602.html)
asThread顺序实现的方法
一个打包函数Fn队列(所谓打包函数就是将回调函数打包后产生的新的函数)

这样产生一个队列，而每产生一个打包函数就push到队列中

在执行的时候setTimout等待后，提供一个fire方法来触发线程取出一个打包函数然后执行，打包函数执行以后回调Thread的fire方法

```
// 打包函数
function package(callback){
    return function(){
        callback();
        // 干其他事情
    }
}
```

# 模块化

[showcase3](https://github.com/mayufo/showcase3)


## global-module

最早没有模板化，当时主要期望通过闭包，来解决全局变量污染的问题
通过自执行函数，将`global`传进去，然后将方法挂载在`global`
但库依然占用全局变量

```js
!function (root) {
  function $() {
    // TODO
  }
  root.$ = $
}(global || window)
```
## global-conflict

```js
// 第一个
!function (root) {
  function $() {
    console.log('i am no.1')
  }

  root.$ = $
}(global)


// 第二个
!function (root) {
  function $() {
    
    console.log('i am no.2')
  }

  root.$ = $
}(global)

function a() {
  $()
}

a() // i am no.2 第一个方法假如是jquery，和第二方法重名，被重复定义，jquery被覆盖
```

如果不想第一个函数被覆盖

```js
// 第一个
!function (root) {
  function $() {
    console.log('i am no.1')
  }

  /**
   * 非冲突
   */
  $.noConflict = function () {
    root.$ = null  // 将$设置为空并return 实例
    return $
  }
  root.$ = $
}(global)

/**
 * 处理冲突
 */
const jQuery = $.noConflict()

function a() {
  jQuery()
}


// 第二个
!function (root) {
  function $() {
    console.log('i am no.2')
  }

  root.$ = $
}(global)


a() // i am no.1
```

## common

当所有的库，都想方设法的在window上挂东西，有可能产生各种冲突
这时候人们尝试用node的common.js规范

```js
// export-fail
exports.word = 'hello'
module.exports = function () {
  console.log(exports.word)
}
```


```js
const fail = require('./module/export-fail')

fail() // hello world
console.log(fail.word)  // undefined
```

> 为什么这里是undefined ?

`exports`是一个对象，后面的方法赋值把对象的指向改变了

## common-mean

```js
const fs = require('fs')
const path = require('path')

function runner(file) {
  const code = fs.readFileSync(path.join(__dirname, file), 'utf-8') // 以utf-8的形式打开文件
  const module = { exports: {} }
  const fn = new Function('module', 'exports', code)
  fn(module, module.exports)
  return module.exports
}

// 等同于 const fail = require('./module/export-fail')
const fail = runner('./module/export-fail.js')

fail() // hello
console.log(fail.word) // undefined

/**
 * fn 到底生成了怎样的函数？
 * 这时候看出来为什么了吗？
 */
function _fn(module, exports) {
  exports.word = 'hello' // 这里依然等于上面设置的空对象
  module.exports = function () { // 这里的module.export被覆盖，不是原来的空对象
    console.log(exports.word) // hello 这里依然可以打印出hello,但是module.exports.word就打印不出来了
  }
}

```

## require-have-name 

`AMD`定义模块的规则

```js
define('./cases/5.require-have-name.js', 'hello world')
```

```js
require._ = {}

// 我们怎么加载一个js？
function require(path, cb) {
    const script = document.createElement('script') 
    script.src = path
    script.onload = function () {
        cb(require._[path])
    }
    document.body.appendChild(script)
}

// 实现一个对应的define
function define(path, factory) {
    let module
    if (typeof factory === 'function') {
        module = factory()
    } else {
        module = factory        
    }
    require._[path] = module
}
```
有名的`define`可以将名字和`module`对应的存起来
在`require`回调的时候对应的取出来

## require-no-name

```js
function require(path, cb) {
    const script = document.createElement('script')
    script.src = path
    script.onload = function () {
        const factory = require.stack.shift()
        let module
        if (typeof factory === 'function') {
            module = factory()
        } else {
            module = factory
        }
        require._[path] = module
        cb(module)
    }
    document.body.appendChild(script)
}
require._ = {}
require.stack = []

// 实现一个对应的define
function define(factory) {
    require.stack.push(factory) // 将方法推导堆栈里面
}
```
无名就情况下`define`只能将module push进一个堆栈里面，
在`require`中取出`module`，对应处理

# 下次作业

[webkit-dwarf](https://github.com/dwarfJS/webkit-dwarf)

做一些东西的时候要说出背景

* `Cache` 方法来记录每个`module`所对应的加载器

* `Def` 存储stack

* `Loader` 用来取值，将事件分发到其他地方，加载模块，加载js

* `makeRequire` 生成require的函数,处理不同的baseURL

* `define` 生成define

* `_resolvePath` 用来处理路径


```
define('xxx', 'sdafsafa')
define('yyy', 'sdgfsga')
define(['xxx', 'yyy'], function (xxx, yyy) {
  return xxx + yyy 
})
```

因此在Def中push了很多的

```
Def.push({
    m: module,
    d: deps,
    f: factory
})
```

Loader.done 处理成功module
Loader.down 处理失败module

homework4 写一个amd加载器

* 循环引用 ？为什么会写出这种代码？

a引用b,b引用c,c又引用a
这意味着b应该在a前，c应该在b前，a应该在c前，这就出现了矛盾
本质来说是其中某个模块没有仔细划分，循环依赖本身不该出现

* 如果判断加载器是AMD规范还是CMD规范

1. 写法差别

CMD -> seajs的规范
AMD -> RequireJS的规范(Commonjs出现后，js社区做的)

```
// CMD
defind(['a'], function (require, module, exports) {
  require('a')
})

// AMD

define(['a', 'requrie'], function (a, require) {

})
```

2. 执行顺序

CMD第一次做require操作执行factory

```js
define(['X'], function (require) {
  // 执行这段的时候 factory才被执行
  const x = require('x')
})
```

factory在AMD是提早执行

```js
define(['X'], function () {
  //  这段之前x已经有值，factory提早执行
  console.log(X)
})

```

不推荐CMD

* 按位非的问题 `~module.indexOf('/')`

~ 返回数值的反码,相当于负数减1
因为`~`是按位非 `'asdf'.indexOf('c')`结果是-1，-1按位取非为0, 而如果能找到就是大于0，大于0的值取反都是负数
~module.indexOf('/') 可以快速判断出module是否包含/ 包含就是true

最好的写法前面加上两个感叹号，

！！~module.indexOf('/') 






