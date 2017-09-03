# homework3

如何做个模型回调两次，捕获错误
```js
// 实现
class Defer {
  constructor() {
    this.doneCbs = [] // success callback
    this.failCbs = [] // fail callback
    this.catCbs = [] // catch callback
  }
 // 触发所有cb
  fire(cbs, arg) {
    cbs.forEach((fn) => {
      try {
        fn.call(this, arg)
      } catch(e) {
        this.fire(this.catCbs, e)
      }
      fn.call(this, arg)
    })
    return this
  }
  done(cb) {
    this.doneCbs.push(cb)
    return this    
  }
  fail(cb) {
    this.failCbs.push(cb)
    return this    
  }
  resolve(arg) {
    this.fire(this.doneCbs, arg)
    return this    
  }
  reject(arg) {
    this.fire(this.failCbs, arg)
    return this    
  }
  catch(cb) {
    this.catCbs.push(cb)
    return this    
  }
}

module.exports = Defer
```

```js
// 测试
describe('defer', function (){
  it('should able to listen a success callback', function () {
    const defer = new Defer
    defer
      // success
      .done(function () {
        console.log('success')
        done()
      })
      // fail
      .fail(function () {
        console.log('fail')
      })
      // error
      .catch(function () {
        console.log('have show error')
      })

      setTimeout(function () {
        defer.resolve()
      }, 100)
  })

  it('should able call sucess callback twic', function (done){
    const defer = new Defer
    let times = 0
    defer.done((res) => {
      console.log(res) // 1  // 2 
      times++
      if(times === 2) done()
    }) 

    setTimeout(function () {
        defer.resolve(1)
        defer.resolve(2)
      }, 100)
  })

  it('should able catch an error', function (done) {
    const defer = new Defer
    defer.done(() => {
      // reference Error； asdfasdf is undefined
      asdfasdf
    }).catch((e) => {
      console.log(e)
      done()
    })

    setTimeout(function () {
        defer.resolve(1)
      }, 100)
  })
})
```

# homework4

karma start 

# 前端模板 & DSL

[前端模板 & DSL](https://github.com/FE-star/2017.8/blob/master/%E4%BB%8E%E5%89%8D%E7%AB%AF%E5%B0%8F%E5%B7%A5%E5%88%B0%E4%B8%AD%E7%BA%A7%E5%B7%A5%E7%A8%8B%E5%B8%88%E7%9A%84%E5%BF%85%E5%A4%87%E6%8A%80%E8%83%BD%E2%80%94%E2%80%94%E5%89%8D%E7%AB%AF%E6%A8%A1%E7%89%88.pdf)

在没有前端模板的时候

![前端模板](https://github.com/mayufo/stuQ-study/blob/master/image/ study_19.png)

早期的前端模板
[javascript-micro-templating](http://johnresig.com/blog/javascript-micro-templating)
通过正则替换

```js
str
          .replace(/[\r\t\n]/g, " ") \\ 回车制表符换为空格
          .split("<%").join("\t")   \\ 拿<%做分隔，再拿制表符做合并
          .replace(/((^|%>)[^\t]*)'/g, "$1\r") \\插值 
          .replace(/\t=(.*?)%>/g, "',$1,'")
          .split("\t").join("');")
          .split("%>").join("p.push('")
          .split("\r").join("\\'")
      + "');}return p.join('');");
```

![前端模板](https://github.com/mayufo/stuQ-study/blob/master/image/ study_20.png)

流程
                (transform)                     (combine data)
Template String ------------>Template Function -----------------> HTML String

> 模板语言的出现体现了什么？

`DOM`的`Api`强大但是不好维护，前端做了那么多View相关的库本质上是为了屏蔽底层DOM操作

模板语言出现了两个方向的发展功能和性能的变化

* 以功能为代表的`handlebars`

[handlebars](http://handlebarsjs.com/)
`handlebars`本事使用词法分析来做的
功能强大，代码量大

 `handlebars`强的`Blocks`扩展能力


```js
<div class="entry">
  <h1></h1>
  <div class="body">
    {{#bold}}{{body}}{{/bold}}
  </div>
</div>
```

```js
Handlebars.registerHelper('bold', function (options) {
  return new Handlebars.SafeString(
    '<div class="myblod">'
      + options.fn(this)
      + '</div>');
  )
})
```
* 以性能为代表的`doT`

首先你得小,（不能做正规词法分析，只能正则替换）
其次，可以线下预编译，称为静态的js文件

还有你可以不走正规的词法分析，走正则
没有什么扩展能力
你可以不使用with
[doT](https://github.com/olado/doT/blob/master/doT.js)
[高性能JavaScript模板引擎原理解析](http://cdc.tencent.com/2012/06/15/%E9%AB%98%E6%80%A7%E8%83%BDjavascript%E6%A8%A1%E6%9D%BF%E5%BC%95%E6%93%8E%E5%8E%9F%E7%90%86%E8%A7%A3%E6%9E%90/)

> 只用正则替换会有什么问题？

  不可扩展
  错误定位和调试做不到


 正规的词法分析，可以做出`Sourcemap`，方便调试而正则无法做到
 词法分析可以知道错误的产生,而不需要运行的时候知道，运行的时候也可以通过`Sourcemap`找到问题

`Sourcemap`就是一个信息文件，里面储存着位置信息。也就是说，转换后的代码的每一个位置，所对应的转换前的位置。当执行代码出错的情况下可以返溯回原来的代码

* MVVM和React的冲击

DOM Template 没有任何东西不能再DOM上被表征的

```html
<div id="app-3">
  <p v-if="seen">看到我</p>
  <ol>
    <li v-for="todo in todos">
      {{todo.text}}
    </li>
  </ol>
</div>
```

  * `html`标签都是闭合的，比较好静态分析
  * 可以把模板直接插入`DOM`，不会有七八的事情发生
  * 不需要写词法分析，一般可以直接用现成的`HTML parser`


* JSX: js可以写在标签

![JSX代码](https://github.com/mayufo/stuQ-study/blob/master/image/ study_21.png)

  * JSX打破了内容和逻辑分离这个web传统的分离方式
  * 尽量减少了概念的引入
  * 通过component化来拆解应用


* web component

以前
```
样式 -----（css）
内容 ----- (html)
逻辑 ----- (js)
```

现在

用Component互相组合嵌套
```
Component
├──css
├──html
└──js
```


```js
class HelloElement extends HTMLElement {
  // name属性监听
  static get observedAttributes () {return ['name'];}
  // 当name属性变化的  
  attributeChangedCallback(attr, oldValue, newVale) {
    if(attr == 'name') {
      this.textContent = `Hello, ${newValue}`;
    }
  }
}

customElements.define('hello-element', HelloElement);
```

```html
<hello-element name="Anita"></hello-element>  // Hello, Anita
```

但我们依然要关注DOM实现细节

Polymer = MVVM + Web Component

中国公司目前没有使用的，需要IE10以上

[Polymer](https://www.polymer-project.org/)

* 找个理由，为啥要弄DSL

`DSL`Domain Specific Language翻译为领域特定语言,为了解决某一类任务而专门设计的计算机语言

一般会有什么阻力

旧的技术能完成目前的需求，为什么要换，增加翻新成本 ？ 
我们已经熟悉了现有的开发方式为什么要换，增加学习成本 ？

* 新技术推广找价值

  * 业务
  * 人员
  * 专业

1. 业务

寻找业务的热点，我们需要好的旗帜

  * 支付宝的DSL事件

我们应不应该有自己的面向开发者的DSL

  * Facebook Licence事件

你用了react的api,如果facebook抄袭了你的东西，你不能告他，因为你用了react

2. 人员

* 从动态运营到动态开发资源

![从动态运营到动态开发资源](https://github.com/mayufo/stuQ-study/blob/master/image/ study_23.png)


全栈不是什么都精通，而是什么都能做，所以要降低开发难度
DSL是降低前端开发难度的良药

* 从分裂走向统一

![从分裂走向统一](https://github.com/mayufo/stuQ-study/blob/master/image/ study_22.png)

3. 技术

* 上层稳定，底层变动不影响上层

2014 jq,angualr好
2015 angular差，React很好
2016 React全家桶复杂，Vue很简单
2017 ？

![底层变动不影响上层](https://github.com/mayufo/stuQ-study/blob/master/image/ study_24.png)


根据不同的人，摆出不同的`PLAN`,和不同的人说出不同的价值

* 价值在何处？

* 说出故事，找到价值，集合资源，一件事是否能做出色跟资源有很大关系
* 一件事情，说不清楚价值，没人任何，做的再好也白做
* 很多事情要跨团队配合才能完成，必须有这样的人来推进事情发生


* 提高效率的方法

  * 番茄工作法
  * 记录每天做的事情
  * 不要并行做某些事情