# 上次作业

[Unix Pipes to Javascript Pipes](https://github.com/imweb/Q.js/issues/17)
[前端数据处理层方法集](https://github.com/miniflycn/db)

* 业务通用处理jQuery.ajax满足不了

  * 登陆态续期(登录态丢失，能否自动弹出登录框登陆后再走之前的流程，需要封装)
  * 通用错误码处理（这里的错误码不是网络的状态码，而是在http做了一个上层协议，通过上层协议的错误码来处理一些事情）
  * 数据本地化（对数据做缓存，性能优化方案）

* 后台（DO)[Data Object] 从数据库里面的数据 --> 前端（VO）[viwe Object]模板里面可以识别
  两种数据不一样，需要有个映射或者转化为view可识别的数据
* 业务通用需求各不相同，如何做一个高扩展的数据处理层库 

可以使用管道模型解决以上

管道模型的基本思想是： do one thing and do it well (完成一个新任务，新建一个任务，而不是在旧程序中添加新特性)

管道的好处

  * 易于扩展
  * 标准化每个程序的输入和输出，让任意符合标准的程序可以串在一起
  * 设计和创造软件而不是一下整体的架构一个系统或软件

* 设计初衷在于解决CGI拉取的通用性问题 ，区分正确和错误逻辑、通用错误逻辑处理、登录态通用处理，但是DB却难以复用，因为每个业务的同用性各不相同，通常我们新起一个业务总是要将DB赋值下来后做大量侵入式修改，才能适用于新的业务要求
`CGI`描述了服务器和请求处理程序之间传输数据的一种标准

![通用错误码而不是状态码](https://github.com/mayufo/stuQ-study/blob/master/image/study_25.png)

`DB场景`处理登录态续登录的问题,相当于http拦截器,可以对数据做一些分流，用管道处理些业务相关的数据，如果业务较多的时候需要定制化，需要有种模型做抽象，可以通过`pipe line`对数据做处理

为什么要数据缓存，如何让页面打开更快

非`server render`页面如何打开更快？

`server render`当服务器接收到请求后，它把需要的组件渲染成 HTML 字符串，然后把它返回给客户端

HTML download -> CSS download -> JS download -> parse JS -> run JS -> AJAX(减掉)-> render

在runjs以后再localStorage里面读取数据，然后再render出来

一般请求使用restful API 100ms ~ 200 ms

1. 如果有多个模块就会有很多请求，可以考虑将多个请求通过`DB`合成一个request发送给后台
2. HTML -> JSONP -> CSS -> JS -> JS parse -> js run -> render (数据预加载)

合并请求那个怎么弄？

graphQL

```
0. 默认，情况下，如果没缓存，成功请求回调一次，如果有请求成功缓存应当回调两次，这样可以保证页面快速渲染，而不需要等数据返回
- 如果`Local Storage`有数据，用`Local Storage`里面的数据,再回调一次，再从线上拿数据，并存在`Local Storage`
- 如果本地有`Local Storage`数据，先`Local Storage`里面的数据，再发送请求更新数据`Local Storage`

1. lazy更新，有缓存的情况下直接读取缓存，只有一次回调，但会发出请求，请求返回后只更新缓存里的数据为最新的，这样，下次再调用就能拿到这次更新的数据了 (数据及时性不强，只是后端直接从缓存中读取数据，也会加个过期时间，过期取线上数据，这个时候数据的存储要带版本号信息)

options.lazy = true

2. 在maxAge内使用缓存，则当发现缓存里的数据小于maxAge，则直接读取缓存，不发出任何请求，否则发出请求读取请求的数据 

options.maxAge = 1000 // 即缓存是1s，也就是说这个单位是ms
（当数据并发量很高的情况，数据存到（memcache或者redis）内存型的缓存数据，然后再丢给用户，几分钟之内不会从service render里面拿数据）
```

refly1✂$_stranger_$✂ppp://123?✂1504940051878

这个key中refly代表一个前缀，stranger代表用户的，`ppp://123?`代表协议，`1504940051878`代表时间戳

value中`v`代表数据的版本号，做了策略的升级，格式不能满足升级的需要，比如加一些字段，就能区别出旧的版本数据，就可以防止数据结构不同，而抛错

大家觉得localStorage是存单key好还是存多key好？

单个key读取速度性能比较慢



localStorage 中的 key 和 value在内存中都保存了一份,提高性能
```js
function save(key, level = 1, value) {
  if (typeof value !== 'object') return // no need cache other type
  _[key] = value // save to memory 存储在内存一份
  save2Local(key, level, value, 0)
}
```

通过等级控制删除数据,还可以使用LRU
`内存管理（LRU）`的一种页面置换算法，对于在内存中但又不用的数据块（内存块）叫做LRU，操作系统会根据哪些数据属于LRU而将其移出内存而腾出空间来加载另外的数据,将老是不用的数据删掉，但是实现较麻烦

```js
function save2Local(key, level, value, removeLevel) {
  value = { r: value, v: PROTOCOL_VERSION }
  setTimeout(() => {
    removeAll(key, level) // 清楚之前所有的localStorage
    const json = JSON.stringify(value)
    if (json.length > 100000) { // 太大不会存 localStorage 2.5M-10M
      Monitor.log('warn', `[API_REPONSE_TOO_BIG]${key}`)
      return // too larget to cache
    }
    const k = `${getTarget(key, level)}${+new Date}`
    try {  
      localStorage[k] = json
      keys.push(k)
    } catch(e) {  // 如果你是level 1的等级数据要存 最多删掉1的等级去存，如果你是2的等级，可以删掉1的等级去存
      if (removeLevel <= level) {
        removeDate(removeLevel)
        save2Local(key, level, value, removeLevel++)
      }
    }
  }, 0)
}
```

错误的上报在`monitor.js`

(new Promise(xxx)).catch() Promise可以通过catch来捕获错误

`jQuery.Deferred` 抛错只能在`onerror`,异步体验很差, 因此对抛错进行包装在`catch.js`,这样可以通过catch函数来捕获到

千万不要用`cookies`做缓存,每次请求都会发送给服务器

通过链表实现LRU
[LRU](https://github.com/rsms/js-lru)


# exercise8

[exercise8](https://github.com/mayufo/exercise8)

```css
// rules
selector {
  // declarations 
  key: value;
  key: value;
}
```

`let prop = match(/^\*?[\w-_]+[\w-_0-9]*/)`
可能以*开头，中间可能是-或者_,数字在后面匹配出现0或者多次

`let val = match(/^[^;]+/)`
以不是；开头

[exercise9](https://github.com/mayufo/exercise9)


`var m = match(/^@import\s+([^;]+);/)`

`var m = match(/^@charset\s+([^;]+);/)`

合并成一个`var reg = new RegExp(`@${name}\\s+([^;]+);`)`
```js
function createAt(name) {
  var reg = new RegExp(`@${name}\\s+([^;]+);`)
  return function () {
    var m = match(reg)
    if(!m) return 
    var ret = {
      type: name,
      name: m[1].trim()
    }
    return ret
  }
}
```

[exercise10](https://github.com/mayufo/exercise10)

需要回溯代码

```js
  /**
   * 更新位置信息
   */
function updatePosition(str) {
  /**
    * TODO
    */
  var lines = str.split('\n')
  lineno += lines.length - 1
  console.log(11, str, lines, 222)
  if(lines.length - 1) {
    column = lines[lines.length - 1].length + 1  // 有换行
  } else {
    column += lines[lines.length - 1].length  // 没有换行
  }
}
```
