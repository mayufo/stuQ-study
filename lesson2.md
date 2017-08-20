---
title: 学习笔记lesson2
date: 2017-08-13 13:12:37
tags: [notebook,study]
toc: true
---


lesson2 

<!-- more -->

# 上一次作业

- 每次作业需要做边界检测，对象和数值有没有超出范围
- 链表的操作
- coverage


使用`karma-coverage`

karma.config.js

```
"scripts": {
    "test": "karma start",
    "report": "find coverage -name lcov.info -print0 | xargs -0 cat | ./node_modules/coveralls/bin/coveralls.js && rm -rf ./coverage"
  },
```
`report`的大概意思是查找`coverage`文件下`lcov.info`文件并输出
`print0`不会给输出的内容增加回车而是增加`null`
`xargs -0` 将查找的文件,以`null`为记录的分隔符
`cat` 展示文件写入后面的coveralls.js中
`rm -rf ./coverage` 删除coverage


`singleRun: !!process.env.TRAVIS` 在travis的环境下有这个变量为true,只跑一遍测试

* 别人作业的coverage [#11](https://github.com/FE-star/homework1/pull/11)
* 自己的作业 [homework1](https://github.com/mayufo/homework1)
* 自己刚开始的一种coverage方案 [coverage](https://github.com/xiaolong2013/coverage/edit/master/README.md)
* 自己的另一个方案coverage [homework1-another-coverage](https://github.com/mayufo/homework1-another-coverage-) 



# exercise4

## test1

```
describe('this', function () {
  it('setTimeout', function (done) {
    var obj = {
      say: function () {
        setTimeout(() => {
          // this 是什么？想想为什么？
          this.should.equal(obj)
          done()
        }, 0)
      }
    }
    obj.say()
  }) 
```

`setTimeout`在一般函数中`this`指向`window`,而这里用到了箭头函数(lambda表达式)

因此`setTimeout`函数外的`this`和`setTimeout`函数内的`this`是一样的，指向相同。
`setTimeout`函数外的this指向obj,因此`setTimeout`函数内的this也是指向`obj`

## test2

```
it('global', function () {
    function test() {
      // this 是什么？想想为什么？
      this.should.equal(global)
    }
    test()
  })
```

没有指定`this`,指向`window`,因为是在`node`环境下，这里指向`global`

## test3

```
  describe('bind', function () {
    it('bind undefined', function () {
      var obj = {
        say: function () {
          function _say() {
            // this 是什么？想想为什么？
            this.should.equal(global)
          }
          return _say.bind(obj)
        }()
      }
      obj.say()
    })
```

`bind`、`call`和`apply`都是改变函数`this`

此处的`obj`因为变量提升，开始为`obj = undefined`,因此在这个函数中`bind(obj)`，实际`bind(undefined)`

在没有指明`this`的情况下，this指向`global`

## test4

```js
it('bind normal', function () {
      var obj = {}
      obj.say = function () {
        function _say() {
          // this 是什么？想想为什么？
          this.should.equal(obj)
        }
        return _say.bind(obj)
      }()
      obj.say()
    })
```

开始定义了对象,为引用类型再运行，这里obj指向的就是obj,因此this也指向obj

# 测试框架和断言库对比

* [egg](https://eggjs.org/)

## 测试框架

`Mocha`功能丰富适合大量测试框架存在，可扩展，适用于大型项目高并发

`AVA`适合简单的程序，跑的快，如果运用在大量测试中，由于是高并发，容易堵塞

## 断言库

`assert`、`express`和`should`

`assert`可以让你的结果报表清晰

`should`是链式的模式，不需要函数调用

`断言`测试脚本的一句话类似

```
expect(add(1, 1)).to.be.equal(2);
```

`断言库`在编写前端测试代码的过程中，测试框架都提供了根据功能，模块来切分测试用例的设计，由于测试代码简单，直接反应API的特点，编写测试代码很大程度取决于断言库

`koa`开源的node服务框架
`egg`阿里的做的类似上面 （洋葱圈模型）


# xhr

```js
const xhr = new XMLHttpRequest()

xhr.onreadystatechange = function () {
  switch (xhr.readyState) {
    case 0:
      // UNSENT (未打开)
      debugger
      break
    case 1:
      // OPENED  (未发送)
      debugger
      break
    case 2:
      // HEADERS_RECEIVED (已获取响应头)
      debugger
      break
    case 3:
      // LOADING (正在下载响应体)
      debugger
      break
    case 4:
      // DONE (请求完成)
      if (xhr.status === 200) {
        console.log(xhr.responseType)
        console.log(xhr.responseText)
        console.log(xhr.response)
      }
      break
  }
}

xhr.open('GET', 'http://y.stuq.com:7001/json', true)
xhr.send(null)
```

# 跨域问题


post 会send一些值
get 会带参数  `http://y.stuq.com:7001/json?name=mayufo`
xhr.open('请求方法'， '请求url', 'true表示异步')

##  jsonp


`http://y.stuq.com:7001/json?callback=ss`

相当于在window上挂一个ss的方法，专门处理返回的内容

```js
window.xxx = function (value) {
  console.log(value)
}

var script = document.createElement('script')
script.src = 'http://x.stuq.com:7001/json?callback=xxx'
document.body.appendChild(script)
```
利用没有跨域限制的script标签加载预设的callback将内容传递个给js


> 为什么要防止跨域？

[浏览器同源政策及其规避方法](http://www.ruanyifeng.com/blog/2016/04/same-origin-policy.html)
[跨域资源共享 CORS 详解](http://www.ruanyifeng.com/blog/2016/04/cors.html)

同源是指协议、域名、端口相同


主要限制

- cookies、 LocalStorage和IndexDB无法读取 (如果没有同源策略,cookie可以共享，网站之间读取登录状态，非常不安全
)
- DOM无法获得 （如果`DOM`可以获取，你可以做个网站用`iframe`嵌套淘宝，我可以通过获取`DOM`来监听你的密码输入）
- AJAX请求不能发送 (公司内网和外网隔离，外网无法内网数据，如果可以请求AJAX请求，内网有个机密的pdf，黑客在外网加一个服务器，内网用户访问这个服务器，通过ajax把pdf下载下来,再上传到他外网的服务器，因为浏览器可以跨域访问)


## CORS

XML2.0 以后出现的新方法

如果你没有设置值，它不会把请求的cookies，带给发送请求的网站

可以设置，这样就会带上请求的cookies

```
xhr.withCredentials = true
```

`caniuse.com` 可以查`xmlhttp`兼容性的网站

对IE8\IE9,需要使用`XDomainRequest`

`CROS` 里面需要设置 `Access-Control-Allow-Origin`


> 如果我们要 `http://*.qq.com` 都支持跨域怎么办？

没有办法返回，或者是*或者是特定的域 
要根据Reference来返回Access-Control-Allow-Origin,它只能设置*或者一个特殊的域名，无法实现*.qq.com

```
module.exports = app => {
  class CrosController extends app.Controller {
    * index(req) {
      // https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS
      this.ctx.set('Access-Control-Allow-Origin', '*') // * 表示所有的域名
      // this.ctx.set('Access-Control-Allow-Origin', 'http://xx.stuq.com')
      // 如果我们要 http://*.qq.com 都支持跨域怎么办？ 没有办法返回，或者是*或者是特定的域 
      this.ctx.body = { msg: 'hello world' }
    }
  }
  return CrosController
}
```
## iframe

`onhashchange`当hash变化


被`iframe`嵌套的代码

```js

var xhr = new XMLHttpRequest()
xhr.onreadystatechange = function () {
    if (xhr.readyState === 4 && xhr.status === 200) {
        var res = JSON.parse(xhr.responseText)
        parent.location.href = `http://y.stuq.com:7001/public/3.html#msg=${res.msg}`
    }
}
xhr.open('GET', 'http://x.stuq.com:7001/json', true)
xhr.send(null)

```

在视图中的

```js
var iframe = document.createElement('iframe')
iframe.src = 'http://x.stuq.com:7001/public/hash.html'
document.body.appendChild(iframe)

window.onhashchange = function () {
  // 小练习，做个工具方法，取出query的值
  console.log(location.hash)
}
```

用正则的方法去location.hash

```js

function getParmFromHash(url, parm) {
    var re = new RegExp("#.*[?&]" + parm + "=([^&]+)(&|$)");
    var match = url.match(re);
    return(match ? match[1] : "");
}
```
## window.name

被iframe嵌套的

```js
var xhr = new XMLHttpRequest()
xhr.onreadystatechange = function () {
    if (xhr.readyState === 4 && xhr.status === 200) {
        console.log(xhr.responseText) // 响应的数据
        window.name = xhr.responseText 
        location.href = 'http://y.stuq.com:7001/public/index.html' // 将嵌套的网页设置为相同的域名，此处就变为了同源策略
    }
}
xhr.open('GET', 'http://x.stuq.com:7001/json', true)
xhr.send(null)
```

在视图中的

```js
var iframe = document.createElement('iframe')
iframe.src = 'http://x.stuq.com:7001/public/name.html'
document.body.appendChild(iframe)
// 第一次打开跨域的
// 回到同域的
var times = 0
iframe.onload = function () {
    if (++times === 2) {
        console.log(JSON.parse(iframe.contentWindow.name))
    }
}

```

## postMessage

通过postMessage传递,数据可以相互传递
被`iframe`嵌套的

```js
var xhr = new XMLHttpRequest()
xhr.onreadystatechange = function () {
    if (xhr.readyState === 4 && xhr.status === 200) {
        parent.postMessage(xhr.responseText, '*')  // * 代表能够接受任何域或者是特定域
    }
}
xhr.open('GET', 'http://x.stuq.com:7001/json', true)
xhr.send(null)
```

在视图中的

```js
var iframe = document.createElement('iframe')
iframe.src = 'http://x.stuq.com:7001/public/post.html'
document.body.appendChild(iframe)

window.addEventListener('message', function(e) {
  console.log(JSON.parse(e.data))
}, false);
```

## require

callback换成define,是和AMD一样的东西

```js
// require(['http://x.stuq.com:7001/json?callback=define'], function (value) {
//   console.log(value)
// })
```

# 跨域五种需要注意的

- `jsonp` 只能`get`请求，不能`post`请求
- `CORS`可以post请求还可以带cookies
- 第三方法和第四方法用`iframe`，数据通信不一样
- 其他的跨域解决反向代理（需要后端的协助）、flash
- 一般常用`jsonp`速度比较快，但是后端不想改可以用`iframe`
- 安全性考虑 可以考虑`CORS` `postMessage`
- 如果考虑到IE低版本，就无法使用`CORS`


`Referer` 就是发起请求，谁来引用的， 后台可以根据这个地址来变成对应的域

# 继承

## es5


- 原型继承

`Animal`需要继承的

```js
function Cat() {}
Cat.prototype = new Animal
Cat.prototype.name = 'cat'
```

- 构造函数

`Animal`需要继承的

```js
function Cat(name) {
  Amimal.call(this)
  this.name = name || 'cat'
}
```

- 组合继承

可以继承原型的方法，且构造函数的方法也被继承

```js
function Cat(name) {
  Animal.call(this)
  this.name = name || 'cat'
}

Cat.prototype = new Aniaml()

// 添加方法
Cat.prototype.say = function() {
  
}
```
在调用`new Aniaml`的时候和`Animal.call`会被创建两次，费内存

- 优化内存

```js
function Cat () {
    Animal.call(this)
    this.name = 'cat'
}

(function() {
  var Super = function() {}
  Super.prototype = Animal.prototype
  
  Cat.prototype = new Super() // 只继承来自Animal.prototype的方法
  // 添加方法
  Cat.prototype.say = function() {
    
  }   
})()
```

```
XXX.ss() // 静态方法
(new XXX()).ss() // 实例方法
```


`new Date` 与 `new Date()`相似，但是后者可以传值

> 为什么代码不分号

现在习惯是2`space`,无分号，因为这样在es6盛行的今天更好些

# Promise

以前如何解决回调地狱 
[callhell](http://callbackhell.com/)
文章提供的方法
- 给匿名函数提供名字，将他们分离保持一种浅代码的风格
- 模块化
后期新的技术可以使用
- promise
- generators
- Async function 
- run-parallel or run-series

## 有promise的情况

```
async_task1()
    .then(async_task2())
    .then(async_task3())
    .then(function () {
        done()
    })
```

## 没有promise的情况

```
async_task1(function () {
    async_task2(function () {
        async_task3(function () {
            
        })
    })
})
```

## 熟悉promise

```
// resolve('成功')
// reject('失败')
(new Promise(function (resolve, reject) {
   setTimeout(()=> {
       resolve('hello world')
   }, 1000)
})).then(successCb, errorCb)
```

```
（new Promise(function (resolve, reject) {
   setTimeout(()=> {
       resolve('hello world')
   }, 1000)
}).then(function (data) {
    console.log(data) // hello world
})
```


```
(new Promise(function (resolve, reject) {
   setTimeout(()=> {
       reject('hello world')
   }, 1000)
})).then(function (data) {
    console.log(data) // hello world
}, function (msg) {
    console.log(msg) // hello world
})
```



```
function async1 () {
    return new Promise((resolve, reject) => {
        setTimeout( function () {
            console.log('run task 1')
            resolve()
        }, 1000 )
    }) 
}

function async2 () {
    return new Promise((resolve, reject) => {
        setTimeout( function () {
            console.log('run task 2')
            resolve()
        }, 1000 )
    })
}

async1() // run task 1
 .then(async2) // run task 2
 .then(function () {
    console.log('done') // done
 })
```

## Promise的一些方法

[Promise](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)

`Promise.all(iterable)`返回新的Promise对象，该Promise对象在`iterable`参数对象里所有的Promise对象都成功的时候才出发成功，一旦任何一个失败，都会立即出发该`Promise`对象失败，并发

`Promise.race(iterable)` 当参数`iterable`里仁轶一个子`Promise`被成功或者失败后，父Promise马上调用子Promise的成功或者失败

`Promise.reject(reason)`返回一个状态为失败的`Promise`对象，给定的失败信息传递给对应的处理方法

`Promise.resolve(value)`返回一个状态给定value决定Promise对象

`Promise.prototype.catch(Rejected)` 能catch错误的Promise


```
new Promise((resovle) => {
    sdsdsdsdsd
}).then( () => {})
  .catch( function (e) {
    console.log(e)
  })
```
不会打断整个流程，而是通过`catch`抛出错误
对于异步的操作，如果没有`catch`，错误只能通过`window.onerror`来得到

# 这次作业

webpack的核心`tapable` 插件机制，以一种类似管道的方式调用插件


