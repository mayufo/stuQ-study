# homework3的解读

回调有两次
- 如果`Local Storage`有数据，用`Local Storage`里面的数据,再回调一次，再从线上拿数据，并存在`Local Storage`
- 如果本地数据，发送请求拿数据

`Local Storage`数据存储会超出，需要处理

这里成功回调需要两遍，而`Promise`只能实现一次回调

写测试用例需要搭一些服务器

# 前端的监控技术
[前端的监控技术](https://github.com/FE-star/2017.8/blob/master/%E4%BB%8E%E5%89%8D%E7%AB%AF%E5%B0%8F%E5%B7%A5%E5%88%B0%E4%B8%AD%E7%BA%A7%E5%B7%A5%E7%A8%8B%E5%B8%88%E7%9A%84%E5%BF%85%E5%A4%87%E6%8A%80%E8%83%BD%E2%80%94%E2%80%94%E5%89%8D%E7%AB%AF%E7%9B%91%E6%8E%A7%E6%8A%80%E6%9C%AF.pdf)



* 要监控什么东西？

  * 业务数据(pv uv 点击率 转化率)

    `pv`页面浏览量或点击量，用户每次刷新即被计算一次
    
    `uv`访问您网站的一台电脑客户端为一个访客
  * 稳定性

    通过数据了解网络是否挂断

  * 性能
  * 错误
  * 用户操作路径（从哪里来到哪里去，跟踪用户操作，大部分从log拿到分析）

* 怎么监控？

  * pv/uv、业务操作上报
  * 跟进上报寻找异常
  * 将页面性能数据上报
  * 将页面产生错误上报
  * 跟踪用户操作路径

* 腾讯的网站做了哪些监控 ？

1. 错误的上报badjs

![错误上报](https://github.com/mayufo/stuQ-study/blob/master/image/ study_3.png)

使用`decodeURIComponent（）`打出来
```
not support crossorigin,Script error.;Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/60.0.3112.101 Safari/537.36&ext[0]={"msid":2204594}&level[0]=1&count=1&_t=1503843125076
```

2. 接口上报-monitors 看看接口的成功率，如果没有到达四个九需要排查

![pv/uv的上报或者是组件的曝光率](https://github.com/mayufo/stuQ-study/blob/master/image/ study_4.png)

3. 用户数据监控-tdw

![业务数据的上报](https://github.com/mayufo/stuQ-study/blob/master/image/ study_5.png)

使用`decodeURIComponent（）`打出来
```
"["opername","ver4","ver5","ver6","refer","from_channel","path","uin","action","ver1","seq_number","obj1","obj2","module","ver9"]&datas=[["Edu","41","","","www.baidu.com","41","afffba-0.6288324808505714",315810688,"loginTiming",430,"1503843119195091544071691897266","",3113,"index",1],["Edu","41","","","www.baidu.com","41","afffba-0.6288324808505714",315810688,"exposure","","1503843125141072810928665545616","banner","CET201706chafen","index",1],["Edu","41","","","www.baidu.com","41","afffba-0.6288324808505714",315810688,"Exposure","","1503843125148006775969858033237","",3113,"index_web_center",1],["Edu","41","","","www.baidu.com","41","afffba-0.6288324808505714",315810688,"exposure","","1503843125178006555329874328431","cates_tab","3113;2020;3111;2022;2004;2012;999","index",1]]&retype=2&_=1503843119149"
```

4. 性能数据的上报 - huatuo
![性能数据的上报](https://github.com/mayufo/stuQ-study/blob/master/image/ study_6.png)

* 上报的基本方案

```
var img = new Image
img.src = `${url}`
```
http://127.0.0.1:7001
因为上报不需要接口返回值且自动跨域，所以一般都用`Image`来完成

上报是报给后端数据，一般是给个`log`信息

[showcase4](https://github.com/mayufo/showcase4)

![report](https://github.com/mayufo/stuQ-study/blob/master/image/ study_7.png)

`http://127.0.0.1:7001/public/report.html`

在log文件就会有一些上报，在服务器端凌晨会有计划任务，查询和统计pv和uv

> 大家会用grep命令来搜索log吗 ？ 

如果打了写上报到log文件需要用grep命令

* 业务数据的上报

1. 人工对操作上报

![人工对操作上报](https://github.com/mayufo/stuQ-study/blob/master/image/ study_8.png)

`http://127.0.0.1:7001/public/handle.html`


```
function report(id, action) {
    var img = new Image
    img.src = `/report?id=${id}&action=${action}`
}

const btn = document.createElement('button')
btn.innerText = 'Click Me'
btn.style = 'width: 300px; height: 200px;'
btn.onclick = function () {
    report(1, 'click-button')
}

document.querySelector('#container')
    .appendChild(btn)
```

2. 百度统计 & Google Analytics (一般网站使用)
  
通过发送带上`cookie`的`image`请求，来定位用户与站点信息 (对整个页面定位)

![Google Analytics](https://github.com/mayufo/stuQ-study/blob/master/image/ study_9.png)

> cookies如果被清除掉了怎么办 ？

给你清除cookies地方,注入一个临时的唯一id,等到那天登录以后，再把临时的id映射到真正的id上,建立关系从而分析出你的一些行为状态

浏览器或者机器能够产生一些唯一识别码，从而达到跟踪的目的

3. 点击截获上报

通过事件冒泡和DOM上的标记找到对应的行动点

`http://127.0.0.1:7001/public/click.html`

需要在`body`上`mark-a`一个位置,在`button`上`mark-b`一个位置

```html
<body mark-a="123">
<div id="container">
    <div class="nav" mark-b="456">
        <button 
            class="btn" 
            style="width: 400px; height: 200px;"
        >hello world</button>
    </div>
</div>
```

```js
const CONFIG = { id: 1, action: 'click' }

    function findMark(target, res) {
        res = res || { a: null, b: null }
        const attributes = target.attributes
        for (let i = 0, l = attributes.length; i < l; i++) {
            if (~attributes[i].name.indexOf('mark-')) {
                res[attributes[i].name.substring(5)] = attributes[i].value
            }
        }
        if (target.parentNode && target.parentNode !== document) {
            findMark(target.parentNode, res)
        }
        return res
    }

    function report(config) {
        const conf = Object.assign({}, CONFIG, config)
        ;(new Image)
            .src = `/click?id=${conf.id}&action=${conf.action}&a=${conf.a}&b=${conf.b}`
    }

    document.body.addEventListener('click', function (e) {
        report(findMark(e.target))
    }, false)
```

> 脑洞时间

1. 基于本地存储数据`log`,必要的时候服务器发起收集
2. 基于`DSL`做切面的事件自动注入，产品可以自定义上报

(使用react发现，react上有所有click的绑定，做一个before或者after的切面，让他做一次上报，甚至做一个配置平台，让产品来配置，那个地方需要上报，编辑的系统就可以自动的编辑进去)

切面编程(AOP)，可以把事件切成一个面，在一个面上面进行一些编程，before做一些事情，after做一些事情

* 业务数据上报我们怎么利用 ？

  * 上报业务的数据，知道pv/uv数据如何，转化率怎样和付款又是什么状态
  * 稳定性的数据上报可以了解到服务是否稳定，掉线情况如何，用户打开情况如何
  * 错误上报，可以了解是否页面非法嵌套，
  * 包括做一次性能优化，性能优化前后来证明性能优化的成效

* 性能数据的上报

1. 打点上报
![打点上报](https://github.com/mayufo/stuQ-study/blob/master/image/ study_10.png)

在指定的位置去`push`一些时间点,在title下push一个0位点，在最后`push`一个`page_css_ready`
![打点上报](https://github.com/mayufo/stuQ-study/blob/master/image/ study_10.png)

在整个js加载完成以后`push`一个`page_js_ready`

在最后再push一个`inline_script_run_time`

使用huatuo去做上报，去分析整个页面，js加载，css加载，不同的网络地区的加载

2. Performance.timing

刚刚的方法无法拿到dns的查询时间，可以了解到白屏时间，握手时间

3. 性能数据能拿来干什么？ 

拿到一些真实用户的数据，比如用户白屏时间，从产品上看，或者从经验值来看是否可以接受？是否需要优化？

发泄一些问题，例如我们对一些地区的支持是不是很差，某些网络环境是不是响应很慢？需不需要推动网络或者CDN的优化
每次做性能优化，能不能做`ABTest`,找到对比情况？ 来验证自己的优化效果？
是否找到参考值，通过流程化的方式让未来我们的项目能够达到一个基准线 ？

`ABTest`，简单来说，就是为同一个目标制定两个方案（比如两个页面），让一部分用户使用 A 方案，另一部分用户使用 B 方案，记录下用户的使用情况，看哪个方案更符合设计

4. 稳定性监控

一般稳定性主要靠后端，前端只是备胎

> 大家觉得应该怎么监控

pv/uv有没有明显的波动，做一些警告，是不是网站挂了如何
做一些接口的监控

# 来自滕雪的错误监控方案 —— badjs

