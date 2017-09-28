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

需要全局安装webpack和webpack-dev-server 最好都安装2.x的版本

- hello webpack

webpack.config.js

```js
module.exports = {
  // 入口文件  
  entry: __dirname + '/src',
  module: {
    rules: [
      {
        test: /\.js$/,
        include: __dirname + '/src',
        use: 'babel-loader'
      }
    ]
  },
  // 输出文件
  output: {
    path: __dirname + '/dist',
    filename: 'bundle.js'
  }
}
```

走到hell-webpack中
输入webpack,会打包出`bundle.js`
在输入`webpack-dev-server`,会看到hello world


- multiple-entries

```js
const path = require('path')

module.exports = {
  /**
   * The base directory 
   */
  context: path.join(__dirname, './src'),
  entry: {
    /**
     * 定义了多个入口文件
     */
    home: './home',
    user: ['./user', './account']
  },
  output: {
    path: path.join(__dirname, './dist'),
    /**
     * 可以映射变量[name]和[hash]
     */
    filename: '[name].bundle.[hash].js'
  }
}
```
`name` 代表自身的文件名，因为入口是两个 就不能写固定的xxx.bundle.js
`hash` 打出文件的md5值，hash可以帮助我们做的版本控制
`timestmp` 上线时间戳, 也可以版本控制，jd常用
`version` 版本

hash、时间戳、version

- loader
```js
module.exports = {
  entry: __dirname + '/src',
  module: {
    rules: [
      {
        test: /\.js$/,  // 匹配哪些文件需要
        include: __dirname + '/src', // 包含哪些做处理
        use: 'babel-loader'  // 增加babel-loader 插件
      }
    ]
  },
  output: {
    path: __dirname + '/dist',
    filename: 'bundle.js'
  }
}
```
babel 的配置，根据配置生成代码
```js
{
  "presets": ["es2015", "stage-0"]
}
```
babel近期又改成了`env`

[官方认可的loader](https://webpack.js.org/loaders/)

- style

三种方式

1. webpack.styles.config.js   

style-loader和css-loader

先用css-loader把css抓取出来，再通过<style>标签插入css代码

`webpack --config webpack.styles.config.js`

```js
var ExtractTextPlugin = require('extract-text-webpack-plugin')

module.exports = {
  context: __dirname + '/src',
  entry: './',
  module: {
    rules: [{
      test: /\.css$/,
      include: [
        __dirname + '/src'
      ],
      use: ExtractTextPlugin.extract({
        fallback: 'style-loader',
        use: ['css-loader', 'autoprefixer-loader']
      })
    }]
  },
  output: {
      path: __dirname + '/dist',
      filename: '[name].bundle.[hash].js'
  },
  plugins: [
    new ExtractTextPlugin('[name].css')
  ]
};
```

style-loader 把css用字符串的方式写入到js里面，然后创建style标签，把对应的css字符串放在style标签里面

优点： 比较快，不会打出多个文件，使用webpack-dev-server直接打开，线上和测试使用

2. webpack.extract.config 上线使用

对应的字符串`extract`出来对应一个新的文件,会生成一个对应js文件和css文件，在html中分别引入两个文件

```js
var ExtractTextPlugin = require('extract-text-webpack-plugin')

module.exports = {
  context: __dirname + '/src',
  entry: './',
  module: {
    rules: [{
      test: /\.css$/,
      include: [
        __dirname + '/src'
      ],
      use: ExtractTextPlugin.extract({
        fallback: 'style-loader', // 备胎
        use: 'css-loader'
      })
    }]
  },
  output: {
      path: __dirname + '/dist',
      filename: '[name].bundle.[hash].js'
  },
  plugins: [
    new ExtractTextPlugin('[name].css')
  ]
}

```

3. webpack.autoprefix.config  // 对css 某些属性增加前缀，增加了兼容能力

```js
var ExtractTextPlugin = require('extract-text-webpack-plugin')

module.exports = {
  context: __dirname + '/src',
  entry: './',
  module: {
    rules: [{
      test: /\.css$/,
      include: [
        __dirname + '/src'
      ],
      use: ExtractTextPlugin.extract({
        fallback: 'style-loader',
        use: ['css-loader', 'autoprefixer-loader']
      })
    }]
  },
  output: {
      path: __dirname + '/dist',
      filename: '[name].bundle.[hash].js'
  },
  plugins: [
    new ExtractTextPlugin('[name].css')
  ]
};
```
 
当有些公共库不需要打进去的时候

- common-chunks

style.css是公共的

```js
var path = require('path')
var CommonsChunkPlugin = require('webpack/lib/optimize/CommonsChunkPlugin') // 打出公用的
var ExtractTextPlugin = require('extract-text-webpack-plugin')  // 处理css

module.exports = {
  context: __dirname + '/src',
  entry: {
    A: './a',
    B: './b',
    C: './c',
  },
  output: {
    path: path.join(__dirname, 'dist'),
    filename: '[name].js'
  },
  module: {
    rules: [{
      test: /\.css$/,
      use: ExtractTextPlugin.extract({
        fallback: 'style-loader',
        use: 'css-loader'
      })
    }, { 
      test: /\.png$/, 
      use: 'file-loader'  // 处理图片
    }]
  },
  plugins: [
    new CommonsChunkPlugin({  // 共同的东西打成commons.js
      name: 'commons',
      filename: 'commons.js'
    }),
    new ExtractTextPlugin('[name].css')
  ]
};
```

打出用公用的commons.js
A.js
A.css
B.js
B.css
C.js
C.css

A页面就可以使用A.js\A.css\commons.js

- vendor

假设有个项目依赖jquery和underscore

```js
var path = require('path')
var webpack = require('webpack')

module.exports = {
  context: __dirname + "/src",  // path.join(__dirname, "/src"); window下要使用path.join
  entry: {
    app: './',
    vendor: ['jquery', 'underscore'], // 使用vendor来先打出
  },
  output: {
    path: path.join(__dirname, 'dist'),
    filename: 'bundle.js'
  },
  plugins: [
    new webpack.optimize.CommonsChunkPlugin({
      name: 'vendor',
      filename: 'vendor.bundle.js'  // 将vendor变为通用的东西
    })
  ]
}
```

生成两个文件一个是bundle.js对应index.js文件
`vendor` 会将`jquery/underscore`打在一起，并且能够让`bundle.js`使用


WTFPL 你想干什么都可以的专利 License  

# loader 练习

[exercise12](https://github.com/mayufo/exercise12)

[How to write a loader?](https://webpack.js.org/development/how-to-write-a-loader/)


https://webpack.js.org/api/loaders/

https://github.com/webpack-china/awesome-webpack-cn

https://github.com/pigcan/blog/issues/1
