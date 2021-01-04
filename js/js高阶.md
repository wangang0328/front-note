# JS 高阶面试

[题](https://juejin.cn/post/6844903969345503240#heading-54)

系统知识点

- https://juejin.cn/post/6844903776512393224#heading-68
- 

## js

### 词法作用域

js只有词法作用域，也就是在定义时而不是在执行时确定的作用域。

```
var value = 1
function foo(){
	console.log(value)
}
function bar(){
	var value = 2
	foo()
}
var() // 1
```

注意： with 和 eval可以修改词法作用域

### 闭包

在js中，实现外部作用域访问内部作用域中变量的方法叫做闭包

### 说说js的垃圾回收(GC)

