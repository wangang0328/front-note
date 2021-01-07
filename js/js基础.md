# js基础

## 一些基础的点

### null 和 undefined区别

- **null表示没有对象，即该处不应该有值**
  - 作为函数的参数，表示该函数的参数不是对象
  - 作为对象原型链的终点`Object.getPrototypeOf(Object.prototype)//null`
- **undefined表示缺少值，就是此处应该有一个值，但是还没有定义**
  - 变量被声明了，但是没有赋值时，就等于undefined
  - 调用函数时，应该提供的参数没有提供，改参数等于undefined
  - 对象没有赋值的属性，该属性的值为undefined
  - 函数没有返回值时，默认返回undefined

使用场景细分如下：

- `null`：

1. `Number(null)` 得到 `0`。
2. 作为函数的参数，表示该函数的参数不是对象。
3. 作为对象原型链的终点。`Object.prototype.__proto__ === null`。

- `undefined`：

1. `Number(undefined)` 得到 `undefined`。
2. 变量被声明但是没有赋值，等于 `undefined`。
3. 调用函数时，对应的参数没有提供，也是 `undefined`。
4. 对象没有赋值，这个属性的值为 `undefined`。
5. 函数没有返回值，默认返回 `undefined`。

### 事件流

事件流描述的是页面接收事件的顺序

注意，并不是所有的事件都有冒泡

- onblur
- onfocus
- onmouseenter
- onmouseleave

### 一句话描述this

就函数而言，this指向最后调用该函数的对象，是函数运行时内部自动创建的一个对象，只能在函数内部使用，就全局而言this指向window

### 规范化

`CommonJS` 规范、`AMD` 规范、`CMD` 规范、`ES6 Modules` 规范这 4 者都是前端规范化的内容，那么它们之间区别是啥呢？

在没有这些之前，我们通过：

- 一个函数就是一个模块。`function fn() {}`
- 一个对象就是一个模块。`let obj = new Object({ ... })`
- 立即执行函数（IIFE）。`(function() {})()`

#### CommonJS 规范

> [返回目录](https://github.com/LiangJunrong/document-library/blob/master/%E7%B3%BB%E5%88%97-%E9%9D%A2%E8%AF%95%E8%B5%84%E6%96%99/JavaScript/%E5%9F%BA%E7%A1%80.md#chapter-one)

这之后，就有了 `CommonJS` 规范，其实 `CommonJS` 我们见得不少，就是 `Node` 的那套：

- 导出：`module.exports = {}`、`exports.xxx = 'xxx'`
- 导入：`require(./index.js)`
- 查找方式：查找当前目录是否具有文件，没有则查找当前目录的 `node_modules` 文件。再没有，冒泡查询，一直往系统中的 `npm` 目录查找。

它的特点：

1. 所有代码在模块作用域内运行，不会污染其他文件
2. `require` 得到的值是值的拷贝，即你引用其他 JS 文件的变量，修改操作了也不会影响其他文件

它也有自己的缺陷：

1. 应用层面。在 `index.html` 中做 `var index = require('./index.js')` 操作报错，因为它最终是后台执行的，只能是 `index.js` 引用 `index2.js` 这种方式。
2. 同步加载问题。`CommonJS` 规范中模块是同步加载的，即在 `index.js` 中加载 `index2.js`，如果 `index2.js` 卡住了，那就要等很久。

#### AMD 规范

> [返回目录](https://github.com/LiangJunrong/document-library/blob/master/%E7%B3%BB%E5%88%97-%E9%9D%A2%E8%AF%95%E8%B5%84%E6%96%99/JavaScript/%E5%9F%BA%E7%A1%80.md#chapter-one)

为什么有 `AMD` 规范？

答：`CommonJS` 规范不中用：

1. 适用客户端
2. 等待加载（同步加载问题）。

所以它做了啥？

可以采用异步方式加载模块。`AMD` 是 `Asynchronous Module Definition` 的缩写，也就是 “异步模块定义”，记住这个 `async` 就知道它是异步的了。

#### CMD 规范

> [返回目录](https://github.com/LiangJunrong/document-library/blob/master/%E7%B3%BB%E5%88%97-%E9%9D%A2%E8%AF%95%E8%B5%84%E6%96%99/JavaScript/%E5%9F%BA%E7%A1%80.md#chapter-one)

CMD (Common Module Definition), 是 seajs 推崇的规范，CMD 则是依赖就近，用的时候再 `require`。

AMD 和 CMD 最大的区别是对依赖模块的执行时机处理不同，注意不是加载的时机或者方式不同，二者皆为异步加载模块。

### ES6 Modules 规范

> [返回目录](https://github.com/LiangJunrong/document-library/blob/master/%E7%B3%BB%E5%88%97-%E9%9D%A2%E8%AF%95%E8%B5%84%E6%96%99/JavaScript/%E5%9F%BA%E7%A1%80.md#chapter-one)

- 导出：

1. `export a`
2. `export { a }`
3. `export { a as jsliang }`
4. `export default function() {}`

- 导入：

1. `import './index'`
2. `import { a } from './index.js'`
3. `import { a as jsliang } from './index.js'`
4. `import * as index from './index.js'`

特点：

1. `export` 命令和 `import` 命令可以出现在模块的任何位置，只要处于模块顶层就可以。 如果处于块级作用域内，就会报错，这是因为处于条件代码块之中，就没法做静态优化了，违背了 ES6 模块的设计初衷。
2. `import` 命令具有提升效果，会提升到整个模块的头部，首先执行。

和 `CommonJS` 区别：

- `CommonJS` 模块是运行时加载，`ES6 Modules` 是编译时输出接口
- `CommonJS` 输出是值的拷贝；`ES6 Modules` 输出的是值的引用，被输出模块的内部的改变会影响引用的改变
- `CommonJs` 导入的模块路径可以是一个表达式，因为它使用的是`require()` 方法；而 `ES6 Modules` 只能是字符串
- `CommonJS this` 指向当前模块，`ES6 Modules` 的 `this` 指向 `undefined`

  `ES6 Modules` 中没有这些顶层变量：`arguments`、`require`、`module`、`exports`、`__filename`、`__dirname`
## 一些面试题

### 下面代码输出？

````
function getPersonInfo(one, two, three) {
  console.log(one);
  console.log(two);
  console.log(three);
}

const person = "Lydia";
const age = 21;

getPersonInfo`${person} is ${age} years old`;

//["", "is", "years old"] Lydia 21
````

如果使用标记的模板字符串，则第一个参数的值始终是字符串值的数组。 其余参数获取传递到模板字符串中的表达式的值！