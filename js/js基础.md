# js基础

## 一些基础的点

### null 和 undefinde区别

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