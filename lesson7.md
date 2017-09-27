lesson 7

# 上次作业

## homework5
需要使用`assert.equal`

`position`记录的位置为什么是第二行开始？
这个和代码语言有关
`position`的作用和代码是为了回溯源代码进行debugger


## homework3

[参考db](https://github.com/miniflycn/db)
`PROTOCOL_VERSION`的意义
前端的存储方式做了变更，旧的版本数据会被清掉
后台同一个接口的数据进行了修改
可以根据这个值去升级，相当于本地把`localstorage`定制一种数据库的格式，对格式进行升级

#  `react`协议问题

Facebook将React的许可改为MIT

![开源许可证](https://github.com/mayufo/stuQ-study/blob/master/image/study_26.png)

- `BSD` 协议 
BSD 代码鼓励代码共享，但需要尊重代码作者的著作权。BSD由于允许使用者修改和重新发布代码，也允许使用或在BSD代码上开发商业软件发布和销售，因此是对商业集成很友好的协议。而很多的公司企业在选用开源产品的时候都首选BSD协议，因为可以完全控制这些第三方的代码，在必要的时候可以修改或者二次开发。
- `MIT` 协议
MIT是和BSD一样宽范的许可协议,作者只想保留版权,而无任何其他了限制.也就是说,你必须在你的发行版里包含原许可协议的声明,无论你是以二进制发布的还是以源代码发布的.
授权协议

BSD和MIT都是讲授予使用和分发权，但要包含协议内容，都没有说专利的问题，

facebook 有增加了`BSD+协议`，如果你使用我的react,我抄袭你，告专利侵权，我可以取消你侵权

利用热点事件站在自己的角度，说出有利于自己话

# 提问

- 项目中如何跨 web/mobile 做一套？

一般做两套，里面会考虑很多兼容的问题，不建议这么做
如果是短期的开发，可以考虑，如果两年以上一定要多端不同界面。

手机app应用的页面一般跟wap是一套

`React Native`是一个JavaScript 的框架，用来撰写实时的、可原生呈现iOS 和Android 的应用。 其是基于React的，而React 是Facebook 的用于构建用户界面的JavaScript 库，但是这里不是给浏览器解释的，而是为移动平台。

`Weex`是一套简单易用的跨平台开发方案，能以 web 的开发体验构建高性能、可扩展的 native 应用，为了做到这些，Weex 与 Vue 合作，使用 Vue 作为上层框架，并遵循 W3C 标准实现了统一的 JSEngine 和 DOM API，这样一来，你甚至可以使用其他框架驱动 Weex，打造三端一致的 native 应用。


`RN`或者`Weex`只能用于简单的，复杂场景还是`h5`

- 未来需要关注的

    - serviceWoker
    - 本地存储
    - localStorage
    - indexDB
    - TCP socket
    - websocket
    - WebRTC
    - https://github.com/WebAssembly/design 提升web汇编技术性能，解决RN Weex
    
# webpack     

[showcase5](https://github.com/mayufo/showcase5)

`hash` 版本控制

`timestmp` 上线时间戳

`version` 版本

babel 又改成了env

[官方任何的loader](https://webpack.js.org/loaders/)


`style-loader`

`css-loader`

`file-loader` 处理图片
 
`Comm`


如果我有css的string，如何用js插进去

创建style 标签插入


https://webpack.js.org/api/loaders/

https://github.com/webpack-china/awesome-webpack-cn

https://github.com/pigcan/blog/issues/1
