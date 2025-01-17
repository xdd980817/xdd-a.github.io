---
title: javascript
date: 2024-03-20
---

## 数组 API
- splice 会修改原数组
- map 不会修改原数组 返回一个新数组
- reduce 累加，返回的是一个值。
- some 一个命中规则 则返回true
- every 每个都命中 返回true 
- filter 过滤出符合条件的项 不会修改原数组
- slice 不会修改原数组 返回截取的项
- foreach 不会修改原数组
- push 后增 返回数组长度
- unshift 前增 返回数组长度
- pop 后删 返回删除的项
- shift 前删 返回删除的项


## Symbol.toPrimitive
- 当进行强制类型转换的时候，会执行这个方法，只有对象转原始的时候会执行
```js
const c = {
    [Symbol.toPrimitive](hint: number | string | default){
        console.log(hint)
        return 33
    }
}

+c // 33
c + 1 // 34
// 他都会执行 Symbol.toPrimitive 这个方法 并将返回值当成计算值。
// hint 是类型，
// 强制数字类型转换时，hint 就是 number
// 强制字符串类型转换时，hint 就是 string
// 强制原始类型转换时，hint 就是 default
```
- 当没有[@@toPrimitive] 属性时会以不同顺序调用 `valueOf` 和 `toString`

## 变量提升 & 函数提升
- 变量提升会将变量的声明提升到当前作用域的最顶端。
- 函数式提升优先级高于变量提升。
:::: code-group
::: code-group-item 变量提升
```js
// - 变量提升
//   - var、const、let 都有变量提升，只是他们提升的方式不同
//     - var 会将声明提升到当前作用域的最顶端

- 例子1
var name = "JavaScript"
function showName(){
  console.log(name);
  if(0){
   var name = "CSS"
  }
}
showName()
这里的name打印的是undefined，是因为他优先会找的是当前执行上下文里的变量，var name = 'css' 会将var name 优先提升到`showName`内部的最顶端，所以打印的是undefined

- 例子2
function test(){
    let name = 1;
    if(true){
        let name = 2
        console.log(name) //2
    }
    console.log(name) //1
}
- 因为 let 有块级作用域，所以 name2 是在if块内部声明的，不会提升到test顶部，所以第二个name读的是test内部的name1

```
:::
::: code-group-item 函数提升
```js
函数的声明方式有两种
1、
   fn();
   var fn = function(){
    console.log(1)
   }
    等同于
    var fn;
    fn(); // 会报错 undefined不是一个函数 fn is not function
    fn = function(){}
    变量形式的声明方式 与变量提升行为一致。

2、
   fn() // 2
   function fn(){
    console.log(2)
   }
```
:::

::: code-group-item 函数提升优先级高于变量提升
```js
function fn(a){
    /**
     * 预编译时，会发生变量提升和函数提升，而函数提升优先级高于变量提升
     * function a(){}
     * function d(){}
     * var a;
     * var b;
     * var d;
     * console.log(a); // function a(){}
     * a 123;
     * console.log(a); // 123
     * console.log(a); // 123
     * console.log(b); // function b(){}
     * d = a;
     * console.log(d); // 123;
     */
    console.log(a);
    var a = 123
    console.log(a);
    function a(){}
    console.log(a);

    var b = function(){}
    console.log(b);

    function d(){}
    var d = a;
    console.log(d);
}
fn(1)
```
:::
::::

## 数组 or 对象 当做参数传递时，直接修改值
- 在 js 中，函数的参数是值传递，但如果是对象的话，那就是引用传递，
- 数组当做参数时，重新赋值，相当于将函数内部的变量重新指向了新的地址，而不会影响到原始数据。
- 对象当做参数时，直接修改对象的其中一个 key 的 value 时，因为是引用传递，他是同一个地址，所以会更改原始数据。
:::: code-group
::: code-group-item 数组
```js
function test(arr){
arr = [1,2,3]
}
const a = [1,2,3]
test(a)
// 当传递的是一个数组的时候，函数内部去重新赋值，并不会影响外部的a，除非直接修改下标对应的值 arr[0] = "a"

```
:::

::: code-group-item 对象
```js
function test(a){
    a.arr = [4,5,6]
}
const a = {
    arr : [1,2,3]
}
test(a) // a.arr [4,5,6]
// 当传递的是一个对象的时候，他指向的是同一个地址，所以修改会影响原始数据。
```
:::

::::


 

## Map 数据结构
- 任何具有 Iterator 接口，且成员是一个双元素数组的数据结构，都可以当做 Map 构造函数的参数。
```js
const map = new Map([["name","张三"]])
map.get("name") // 张三


const set = new Set([
  ['foo', 1],
  ['bar', 2]
])

const map1 = new Map(set)
map1.get("foo") // 1

```
- map的key 如果是引用类型的话，他们的引用地址应该是同一个，否则的话会获取不到
```js
const map = new Map()
map.set(['a'],1);
map.get(['a']) // undefined 尽管['a'] 都是一样的 但是他们在内存中的地址是不同的 所以获取不到。

```
- 同一个key 的 map 后者会覆盖前者
```js
const map = new Map()
map.set(1,true);
map.set(1,false)

map.get(1) // false


```
- map 去访问一个不存在的键时，会得到undefined
```js
const map  = new Map()
map.get('asdasd') // undefined

```
- 如果 Map 的键是基本类型时，只要保证键严格相等，那么就会被视为同一个键，NaN尽管不相等，但是 Map 也将视为同一个键
```js
const map = new Map()
map.set(-0,1)
map.get(+0) // 1

map.set(true,1)
map.set('true',2)
map.get(true) //1
map.get('true') //2

map.set(NaN,1)
map.get(NaN) // 1

map.set(undefined,1)
map.set(null,2)

map.get(null) // 2
map.get(undefined) //1



```


## Set 数据结构
- set 构造函数可以接受一个数组或者具有 iterable 接口的 其他类型
- set 通过 add方法添加值，并且不会重复
```js
const set  = new Set()

[1,2,3,4,1].forEach(v=>set.add(v))
set // [1,2,3,4]

```
- set 添加值的时候 是通过 === 来比较的，他不会进行类型转换。NaN 除外， set认为他是相等的。


## Module 语法
```js
// 运行时加载，只有运行时才能得到fs对象，没有办法在编译时做静态优化
// 会加载fs整个模块
const { stat, exists } = require('fs') // commonJS 模块
// 等同于
const fs = require('fs')
const stat = fs.stat
const exists  = fs.exists

// 从fs模块上加载2个方法 其他不加载。
// 在编译时就加载了，比commonJS 要快
import { stat, exists}  from 'fs' 
```
- export 必须提供对外的接口，不能直接输出值。
```js

export 1

var m = 1
export m 
// 以上两种方式都会报错，

// 正确的写法
export default m
export { m }

```

- import 命令 输入的变量都是只读的，因此无法更改他，但是如果 输入的变量是一个对象的话 那么更改他是合法的
```js
import {a} from './test'
a = {}  // 报错

a.foo = 1 // 正确
```

- import 具有提升效果，会提升到整个模块的最顶部。
- import 是静态执行的，因此无法进行表达式与变量操作，这些只能在运行时的才能得到结果的操作是不被允许的
```js
import {'f' + 'po'}  from 'a'

var module = 'test'
import {a} from module

if(true){
    import {a} from 'test'
}else{
    import {b} from 'test'
}
// 以上操作都是不被允许的，会报错。
```

- export default 本质就是输出一个叫做 `default` 的变量，所以他后面不能跟变量声明语句
```js
// good
var a = 1
export default  a;

export var a = 1;

export default 43

// bad
export default var a = 1;
export 43
```

- import() 可以进行动态的加载，什么时候执行到他，就什么时候加载,他返回的是一个promise对象
```js
import("/test.js").then(module=>{
    module.xxx
}).catch(err=>{
    xxx = err.msg;
})


```

## ES6 模块 与 CommonJS 模块的区别
- CommonJS 模块 输出的是一个值的拷贝， ES6 模块输出的是值的引用
    - CommonJS 一旦输出了这个值，模块内部的变化，不会影响到这个值。
    - 加载了一个原始类型的值，会被缓存，除非写成函数形式，才可以得到修改后的值。
```js
// lib.js
var count  = 0;
function setCount(){
    return count++;
}

module.exports = {
    count,
    setCount,
    get getCount(){
        return count
    }
}


// main.js
var md = require('lib.js')
md.count // 0
md.setCount() 
md.count // 0
md.getCount() // 1

```
    - ES6 模块 遇到 `import` 时，就会生成一个只读的引用，到脚本真正执行的时候，会去被加载的模块中获取值。
    - 因此 ES6 是动态引用的，不会被缓存。
    - 所以，ES6 模块， 如果对变量进行重新赋值的话，是会报错的，因为地址是只读的不可更改。
```js
export var count  = 0;
export function setCount(){
    return count++;
}


// main.js
import { count, setCount} from 'lib.js'
count // 0
setCount() 
count // 1

```
- CommonJS 模块 是运行时加载，ES6 模块是编译时输出接口
- CommonJS 模块的 `require()` 是同步加载模块， ES6 模块的 `import` 是异步加载，有一个独立的模块依赖的解析阶段。
- CommonJS 模块想要加载ES6模块
```js
(async ()=>{
    await import("test.mjs")
})()

```
- ES6 模块 加载 CommonJS 模块，只能整体加载，不能加载单一项。
  - 因为 ES6 需要支持静态代码分析，而 CommonJS 模块 输出的是一个对象，无法被静态分析，所以只能整体加载。
```js
// good
import test from 'test'

// bad
import {test } from 'test'

```
- CommonJS & AMD 模块都是运行时，，ES6 是编译时就能确定要输出，输入的变量等


## sessionStorage 可以在多个标签页共享数据吗？
- sessionStorage 不可以在多个标签页共享数据，但是如果是用过 `window.open` 打开的标签页且不是 `__blank` 的时候，他会复制一份 sessionStorage 到新的页面。


## Promise 介绍
- 

## Promise面试题
- 当调用promise的then方法的时候 会做两件事情，
  - 1、如果promise 是已完成的状态，那么then的回调函数会被推入微队列。
  - 2、看是不是会返回一个新的promise，返回的promise 是什么状态，取决于回调函数的运行过程，如果回调函数运行过程没有报错，那就是 `fulfilled` 完成，反之 `rejected`失败
- 当 then方法中 返回了一个promise的时候，then的状态取决于 返回的这个promise的状态。
  - 如何保持状态一致呢？
    - 1、他把保持状态一致的事情，放到一个then里面，然后将这个then推入微队列

- 1、p0因为是resolve 已完成的状态，所以将 then 推入微队列
    - 微队列有`p0`
- 2、res 因为 前一个p0 还没有执行完成，还是 pending 状态 所以先不管 res
- 3、p1 因为是resolve 已完成的状态，所以将then 推入微队列
  - 微队列有`p0`、`p1`
- 4、后续不管，因为p1 状态是 pending
- 5、微队列中取出p0，因为返回了一个 promise 所以p0的状态取决于 返回的promise 并且 将其放入then中 也就是 p4.then(()=>完成p0) 
  - 打印0
  - 微队列中有 `p1`、 `p4.then(()=>完成p0)`
- 6、因为p0还是pending 所以 res 还不管， 接下来取出p1，p1执行完成之后状态变为已完成，将p2推入微队列
  - 打印1
  - 微队列中有 `p4.then(()=>完成p0)`、`p2`
- 7、取出 `p4.then(()=>完成p0)` p4状态是resolve，将`完成p0` 推入微队列
  - 微队列有 `p2`、`完成p0`
- 8、 取出`p2`，执行完成之后状态变为已完成，将p3推入微队列
  - 打印2
  - 微队列有 `完成p0`、`p3`
- 9、取出 `完成p0`, p0执行完成，将 `res` 推入微队列
  - 微队列中有 `p3`、`res`
- 10、取出 `p3`，p3执行完成之后状态变为已完成,将p5推入微队列
  - 打印3
  - 微队列中有 `res`、`p5`
- 11、取出`res`
  - 打印 4
  - 微队列中有 `p5`
- 12、 取出p5
  - 打印 5
- 最终打印顺序为 0、1、2、3、4、5
```js
Promise.resolve().then(()=>{
    console.log(0)
    return Promise.resolve(4)
}).then(res=>{
    console.log(res)
})


Promise.resolve().then(()=>{
    console.log(1)
}).then(()=>{
    console.log(2)
}).then(()=>{
    console.log(3)
}).then(()=>{
    console.log(5)
})
```


## class 基础知识
- static 声明的变量和方法 属于静态的，只能被类调用，不能被实例调用，实例调用会报错。
- class 内部 可以通过 get  set 关键字，来给字段的读写增加拦截
- 私有属性，只能在类的内部使用，外部无法使用，通过#来定义 例如: this.#count = 0;
- 父类的所有方法都会被子类继承，除了私有属性和方法，子类调用的话会报错。
- super 关键字可以做为方法，表示调用父类的构造函数，也可以作为对象，在普通方法中指向父类的原型，在静态方法中，指向父类
```js

class Parent {
    p(){
        return 1
    }
}

class Child extends Parent {
    constructor(){
        super()
        console.log(super.p(),'父类的p')
        console.log(super.prototype,'父类') // undefined
    }

    static test(){
        console.log(super.prototype) // {p:f}
    }
}


```

##  instanceof typeof 有什麼區別
- instanceof 只能比较对象，判断依据是原型链查找
- typeof 将 `null`、`Object`、`Array`、`promise`等都看做 `object`

## 跨域 cookie 失效的原因与解决办法
- 浏览器同源策略中提到了 每个源都有自己独立的存储空间，所以他们之间不能互相读写。
- 我们可以通过 `Access-Control-Allow-Credentials` 请求头进行配置。
```js
/** 
 * fetch 的解决办法
 */
credentials: "include" // 告知浏览器可以将cookie暴露给前端。

/**
 * Xhr 的解决办法
 */
XMLHttpRequest.withCredentials:true
```

## meta 标签
> `name` 属性，meta 表示文档级别的，应用于整个页面。
> `http-equiv` 属性 meta 表示编译指令，提供的信息，与HTTP请求头类似。
> `charset` 属性，meta 表示字符集声明，告诉文档使用哪种字符编码。
> `itemprop` 属性，meta 表示用户定义的元数据。
- 举例说明
```html
<!-- name 属性，与 content 连用， -->
<!-- 作者是谁？  xdd@github.com -->
<meta name="author" content="xdd@github.com"/>
<!-- 页面的描述 -->
<meta name="description" content="学习文档"/>
<!-- 仅供移动端可用， -->
<meta name="viewpoint" content="width=device-width, initial-scale=1.0"/>
<!-- 指定双核浏览器，默认的渲染方式 -->
<meta name="renderer" content="webkit" />
```