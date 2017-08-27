# 课前测试

*  如何实现一个三栏布局，要求两边固定宽度，中间自适应

[两列布局和三列布局使用双飞翼、圣杯和flex总结](http://www.cnblogs.com/mayufo/p/7299255.html)

- 圣杯布局
- 双飞翼布局
- flex布局

* 说一下对 `Javascript` 变量提升的理解

在es6没有出现的时候，`var`定义变量，没有赋值的时候初始为`undefined`

1. `var`的声明
```js
function fn () {
    var x = 1
    var y = 2
}
fn()
```
* 进入fn,为fn创建一个环境
* 找到fn中所有用var声明的变量，在这个环境中`创建`这些变量x和y
* 将这些变量`初始化`为`undefined`
* 开始执行代码
* x = 1 将x变量`赋值`为1
* y = 2 将y变量`赋值`为2

2. `fn`的声明

```js
fn2()

function fn2() {
    console.log(2)
}
```
* 找到所有function声明的变量，在环境中`创建`这些变量
* 将这写变量初始化并赋值为`function() {console.log(2)}`
* 开始执行代码`fn2()`

3. `let`的声明

```js
{
    let x = 1
    x = 2
}
```
* 找到所有用let声明的变量，在环境中`创建`这些变量
* 开始执行代码（还没有初始化）
* 执行x=1,将`初始化`为1（这并不是一次赋值，如果代码是let x, 将x初始化为undefined）
* 执行x=2,对x进行`赋值`

4. `let`暂时死区

```js
let x = 'global'
{
    console.log(x) // Uncaught ReferenceError: x is not defined
    let x = 1
}
```
* console.log(x)中的x指的是下面的x,而不是全局的x
* 执行log时x还没有`初始化`，所以不能使用（也就是所谓的暂时死区）  

[我用了两个月的时间才理解 let](https://zhuanlan.zhihu.com/p/28140450)

* `let`声明的变量的作用域是块级的
* `let`不能重复生命已存在的变量
* `let`有暂时死区，不会被提升

* `for( let i = 0; i< 5; i++) { 循环体 } `在每次执行循环体之前，js引擎会把i在循环体上下文中重新声明及初始化一次
* `var`的声明会在代码执行之前就将创建变量，并将其初始化为 undefined
* `function`声明会在代码执行之前就创建、初始化并赋值
* 用`let`声明的变量，在环境中创建这些变量，但不做初始化，let的创建过程被提升，但是初始化没有提升
* `const`只有「创建」和「初始化」，没有「赋值」过程。


* 下面代码会打印什么? 为什么?
```js
function A() { this.name = 'Daniel' } 
A.prototype.test = function () { 
    setTimeout(function () { console.log(this.name) }, 1000) } 
    var a = new A();
    a.test();
```

`this`永远指向函数运行时所在的对象，而不是函数被创建时所在的对象，谁调用，指向谁

```js
var name = '全局';
function getName () {
  var name = '局部';
  return this.name;
}
console.log(getName()); // 全局
```

`getName()`在window中调用，因此this指向window,this.name也就是全局

没有明确的当前对象this指向window,比如`setTimeout`或匿名函数,

```js
var name = 'Bob'
var nameObj = {
    name: 'Tom',
    showName: function () {
        alert(this.name)
    },
    waitShowName: function () {
        setTimeout(this.showName, 1000)
    }
}

nameObj.waitShowName()  // Bob
```
`this.showName`当做函数调入setTimeout的时候不属于任何对象，因此指向`window`,也可以理解`setTimeout`里面的this指向`window`

回到给的例子

这里打印的是window.name的值，应该是空字符串
这里的window.name是有默认值的，如果之前设置过，就会打印之前设置的值，只要窗口不被关闭，就不会改变，可以`解决iframe跨域问题`

* setTimeout 和 setInterval 有什么差别? (自己写的)

setTimeout 是运行指定的毫秒数后，函数被添加到事件队列中
setInterval 以指定的毫秒数为周期，将函数添加到事件队列中

* 如何实现类似 jQuery 的链式调 ?
`return this`

```js
class Core {
    constructor() {}
    say(word) {
        console.log(word)
        return this
    }
}
const core = new Core
core.say('hello').say('wore')
```
* Yslow 和 PageSpeed 你知道怎么用吗?你记得其中多少规则? 
    * 性能优化，可以建议
    * 建议安装`PageSpeed`,可以测试自己的页面，会给一些基础的优化
    * 合并资源
    * 利用缓存
    * 图片压缩
    * etag 通过文件版本标识，方便服务器判断请求的内容是否有更新，如果没有就响应 304 ，避免重新下载
    * max-age 指定缓存的有效期

* HTTP请求头中，状态码分别是:200、301、302、304 分别代表什么意思? 

    * 200 代表请求成功
    * 204 无内容 服务器成功处理了请求，但没有返回任何内容
    * 3xx 表示要完成请求，需要进一步操作。 通常，这些状态代码用来重定向
    * 301 (永久移动) 请求的网页已永久移动到新位置。 服务器返回此响应(对 GET 或 HEAD 请求的响应)时，会自动将请求者转到新位置
    * 302 (临时移动) 服务器目前从不同位置的网页响应请求，但请求者应继续使用原有位置来进行以后的请求
    * 303 (查看其他位置) 请求者应当对不同的位置使用单独的 GET 请求来检索响应时，服务器返回此代码。
    * 304 (未修改) 自从上次请求后，请求的网页未修改过。 服务器返回此响应时，不会返回网页内容
    * 4xx (请求错误) 这些状态代码表示请求可能出错，妨碍了服务器的处理。
    * 5xx (服务器错误)这些状态代码表示服务器在尝试处理请求时发生内部错误。 这些错误可能是服务器本身的错误，而不是请求出错。
 
* PNG 和 JPG 有什么差别? （背一下）

图片格式 | 支持透明 | 动画支持 | 压缩方式 | 浏览器支持 | 相对原图大小 | 适应场景
-----|------|------|------|-------|--------|-----
baseline-jpeg | 不支持 | 不支持 | 有损 | 所有 | 由画质决定 | 所有通用场景
progressive-jpeg | 不支持 | 不支持 | 有损 | 所有 | 由画质决定 | 所有通用场景, 渐进式加载
gif | 支持 | 支持 | 无损 | 所有 | 由帧数和每帧图片大小决定 | 简单颜色，动画
png | 支持 | 不支持 | 无损 | 所有 | 由png色值位数决定 | 需要透明时
webp | 支持 | 不支持 | 有损 | Chrome、Opera | 由压缩率决定 | 复杂颜色及形状，浏览器平台可预知
apng | 支持 | 支持 | 无损 | Firefox、Safari、iOS Safari | 由每帧图片决定 | 需要半透明效果的动画
svg | 支持 | 支持 | 无损 | 所有(IE8以上) | 由内容和特效复杂度决定 | 简单图形，需要良好的放缩体验，需要动态控制图片特效
bpg（使用较少） | 支持 | 支持 | 有损 | 不支持，需要js解码 | 由画质决定 | jpeg上需要极限优化的场景

[前端图片优化机制](http://imweb.io/topic/568b20194c44bcc56092e415)

* 事件冒泡和事件捕获有什么区别? （自己写的答案）

事件冒泡是一种事件行为当触发事件后，事件会像涟漪一样，从小范围到大范围，元素被逐层触发
事件捕获是当触发这个事件后，从包围这个的根元素到具体触发的元素，从远到进，元素逐层被触发

Dom标准事件流的触发的先后顺序为：先捕获再冒泡，即当触发dom事件时，会先进行事件捕获，捕获到事件源之后通过事件传播进行事件冒泡。

* 你遇到过兼容性问题吗?最令你影响深刻的兼容问题是什么?（自己写的答案）
移动手机的端 安卓2.3与2.4的兼容性，华为手机

工程类(6懂得2)

* 你使用过构建工具来编译前端代码吗?你使用过 Grunt、Gulp、webpack、FIS 其中之一吗?
会讲webpack

* CDN是什么?为什么能资源加速?

![](https://raw.githubusercontent.com/mayufo/mayufo.github.io/master/2017/08/06/%E5%89%8D%E7%AB%AF%E5%AD%A6%E4%B9%A0/%C2%A0study_1.png)

    * 用户从local DNS看下，那个IP比较好
    * 如果本地的DNS没有找到去向上递归，去查询腾讯的DNS，查到最佳的接入IP，这个最佳可能跟你是同个线路的服务器
    * 用户拿IP去查CDN的节点，节点内部有缓存
    * 如果第一次请求，会去源服务器发送请求找到后存在CDN的缓存里面，分热缓存和冷缓存
    * 如果直接命中，就直接传给你了

CDN的动态加速？

我们请求一个文件到CDN的节点，CDN不走缓存，直接走源站，源站把数据返回给CDN，CDN再返回给用户，看起来线路是拉长的，CDN只是起到代理的作用，因为好的CDN和源服务器会有一些专用线路，因此得反而会快    

* 你使用过React、Vue、Angular其中之一吗? 

目前比较流行React和Vue

* 你知道前端怎么样能拿到DNS的查询时间吗?

h5以后的方案 有个`performance.timing`的接口,可以做一些性能监控，握手时间、onload时间...
![](https://raw.githubusercontent.com/mayufo/mayufo.github.io/master/2017/08/06/%E5%89%8D%E7%AB%AF%E5%AD%A6%E4%B9%A0/%C2%A0study_2.png)


* 你能拿 localStorage 来做些什么? 

localStorage可以用来在浏览器存储数据，记录状态
各种缓存
localStorage不可信，不能存重要数据，可能会损坏丢失，需要做好容错

* 设计一个广告系统，类似广告联盟，如何对没有登录态的用户做跟踪?（没有讲）

* CDN怎么解决https和运营商的劫持

如果有运营商劫持代码注入是无法解决，而https运营商无法被劫持的

# 前端测试

javascript测试基础

- 单元测试 mocha should.js对软件中最小可测单元测试
- UI测试 karma
- CI集成测试 tavis

[exercise1](https://github.com/FE-star/exercise1)

text1 
```js
describe('Array', function() {
  describe('#indexOf()', function() {
    it('should return -1 when the value is not present', function() {
      assert.equal(-1, [1, 2, 3].indexOf(4)/* 填空题 */)
    })
  })
})
```

text2
```js
describe('assert', function () {
  it('a和b应当深度相等', function () {
    var a = {
      c: {
        e: 1
      }
    }
    var b = {
      c: {
        e: 1
      }
    }
    // 修改下面代码使得满足测试描述
    assert.deepStrictEqual(a, b)
  })
```
[NodeJS Assert API](http://nodejs.cn/api/assert.html)
`deepEqual` 深度相等，相等运算符（==）比较, 只测试可枚举的自身属性，不测试对象的原型、连接符、或不可枚举的属性
```
assert.deepEqual(Error('a'), Error('b'));  // 不会抛出 AssertionError,Error不可枚举
```
`deepStrictEqual` 参与expectd严格的深度相等
与 `assert.deepEqual()` 大致相同，但有三个区别
    * 原始值使用全等运算符（===）比较，
    * 对象的类型标签要求相同，
    * 对象的原型也使用全等运算符比较

text3
```js
 it('可以捕获并验证函数fn的错误', function () {
    function fn() {
      xxx;
    }
    // 修改下面代码使得满足测试描述
    assert.throws(
      fn,
      /xxx is not defined/
    )
  })
```
也可以用`try...catch`

```js
try{
    fn()
} catch(e) {
    assert.equal(e.message, 'xxx is not defined')
}
```
在开源项目中发现一个`bug`,不仅要解决bug,而且需要测试用例来描述

## [exercise2](https://github.com/FE-star/exercise2)

大数相加思路

- 将需要相加的数用`split`拆为数组
- 由于位数不一定相等可以使用`reverse`对数组进行倒序
- 判断出那个位数比较多的作为循环的结束，通过循环，按位相加,并加进位符flag
- 如果某位大于9，则次位需要减10，并设置进位符`flag = 1`
- 而加完flag，需要重写设`flag = 0`
- 如果最后一位相加`flag = 1`，需要`push(1)`
- 最后`reverse()`后再`join()`

```js
function add(a, b) {
    var aList = a.split('').reverse();
    var bList = b.split('').reverse();
    var flag = 0;
   var max = Math.max(aList.length, bList.length);
    
    var cList = [];
    
    for (var i = 0, len = max; i < len; i++) {
        
        var tmp = ( +aList[i] || 0 ) + ( +bList[i] || 0 ) + flag;
        console.log(tmp)
        flag = 0;
        if(tmp > 9) {
            tmp -= 10;
            flag = 1;
            console.log(tmp)
        }
        cList.push(tmp);
    }

    if(flag) {
        cList.push(1)
    }

    return cList.reverse().join('')
}

module.exports = add

```

* [Travis CI Node](https://docs.travis-ci.com/user/languages/javascript-with-nodejs/)

1. 先打开`CI`,注册或者登陆，在`Accounts`里面，打开需要`CI`的仓库

2. 在本地生成`.travis.yml`
```
language: node_js // 使用的语言
node_js:
  - "7"  // 使用的node版本
before_install:
  - npm install -g mocha // 使用前需要安装
```
`CI`可以自动运行
`npm install` 
和你在`package.json`里面设置的test命令

3. 在`package.json`中`repository`的地址是否正确

* 什么情况下去做单元测试 ？

- 公共组件需要
- 开源项目需要
- 会测试用会在大公司加分，node服务

[一些test的框架](https://github.com/sindresorhus/awesome-nodejs)

`集成测试`除了单元测试，还能跑所有单元或者模块联合起来的测试


* should.js
不需要再各个模块里面`require('should')`
需要创建`mocha.opts`
```
--require should
```
这样会对所有的funtion、object的原型链注入`should`,而should里又有一些方法
[should API](https://shouldjs.github.io/)

```js
add('42329', '21532')
      .should.equal('63861')
```

* 测试率

测试对原始代码的覆盖率，超过`70%`就很好

可以使用[node-coveralls](https://github.com/nickmerwin/node-coveralls)

可以参考
[url-extract](https://github.com/miniflycn/url-extract)

## [exercise3](https://github.com/FE-star/exercise3)

`before`测试开始
`after`测试结束

karma可以启动浏览器，并调用mocha可以单元测试

```js
$.ajax('https://raw.githubusercontent.com/FE-star/exercise1/master/test/test.js', function () {
    corssDomain: true //跨域请求 需要标识出来
}).success(function () {
    done()
})
```

jquery发现请求不是`localhost`,它会在`XMLHTTPRequest2`里面`Access-Control-Allow-Origin: *`,`*`表示所有请求都跨域

测试用例2000ms没有返回会报错

`done()`异步测试必须有回调才能告诉测试框架，异步测试成功

# 下次作业

homework1