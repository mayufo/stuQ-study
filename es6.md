# 声明变量

## 声明变量关键字

var、let、const

## 解构赋值

* 数组
```js
let arr = [miaov, ketang, leo]
let [c, d, e] = arr
console.log(c, d, e) // miaov, ketang, leo
```
* 对象
```js
let obj = {foo: 1, bar: 2}
let {foo, bar} = obj;
console.log(foo, bar) //1 2
let {foo, bar, miaov:abc } = obj  // 可以没有顺序，这里给miaov起别名
```
* 字符串

* 函数的解构赋值

## 字符串方法

* repeat

```js
let str = '我要学习'
console.log(str.repeat(3)) // 我要学习我要学习我要学习  这里不能小于0，如果是0 就是没有了
```

* 模板引擎

```js
let html2 = ``
```
## 箭头函数

返回对象 
```js
let func3 = （n, m）=> ({resut : n+m})
```

函数体内的this => 绑定定义时所在的上下文

## 函数的扩展

... 把所有的实参都放在数组里面，可以取代`argument`

```js
let arr = [1,2,3,4]
let arr2 = [4,5,6]
arr.concat(arr2)
let arr3 = [...arr, ...arr2]; //和上面的效果一样

let arr4 = [4,5,6,7,8,100,0]
Math.max.apply(null, arr4) // 100
Math.max.call(null, ...arr4) // 100
```

* includes 找数组是否包含某一项
```js
let arr = [1,2,3,4,5];
arr.include(5)  // true 可以在数组里面找NaN,但是indexOf不能找到
```

* Array.from()  // 将类数组转为数组, 第二个参数是函数

Array.from(lis, function (item) {
  return item.innerHTML
})

## 对象的扩展

变量名和属性名相同，可以省略其中一个
函数可以不用写function

定义属性可以使用[]进行计算
```js
let a = 5
let obj = {
  // a:a
  a
}
```

Object.is(1,1)  // true
Object.is(1,2)  // false

`Object.assign`  // 合并对象 浅拷贝