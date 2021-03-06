# JS 高阶面试

[题](https://juejin.cn/post/6844903969345503240#heading-54)

系统知识点

- https://juejin.cn/post/6844903776512393224#heading-68
- 

## js

### 执行上下文

- **变量提升：**

  所谓的变量，是指javascript代码执行过程中，JavaScript引擎吧变量的声明部分和函数的声明部分提升到代码开头的行为。变量被提升后，会给变量设置默认值，这个默认值就是我们熟悉的undefined

- **JavaScript执行的流程**

  实际上变量和函数声明在代码里的位置是不会改变的，而且是在编译阶段被JavaScript引擎放入内存中。

  一段js代码需要先编译， 然后执行

- **执行上下文是JavaScript执行一段代码时的运行环境**，

  包括：

  - 全局执行上下文

    当js执行全局代码的时候，会编译全局代码并创建全局执行上下文，而且在整个页面的生命周期内，全局执行上下文只有一份。

  - 函数执行上下文

    当调用一个函数时，函数体内的代码会被编译，并创建函数执行上下文，一般情况下，函数执行结束后，创建的函数执行上下文会被销毁。

  - eval执行上下文

    当使用eval函数时候，eval的代码也会被编译，并创建执行上下文

  比如： 调用一个函数时，就会进入这个函数的执行上下文，确定该函数在执行期间用到的诸如 this、变量、对象以及函数等。

  在执行上下文中存在一个**变量环境的对象**，该对象中保存了变量提升的内容

- **调用栈**

  **调用栈就是用来管理函数调用关系的一种数据结构**

  - 函数调用： 函数调用就是运行一个函数，具体使用方式是使用函数名称跟着一对小括号。

    ````javascript
    var a = 2
    function add(){
        var b = 10return a+b
    }
    add()
    ````

    在执行到函数add()之前，JavaScript引擎会为上面这段代码创建全局执行上下文，包含了声明的函数和变量（变量环境中）.

    1. 从全局执行上下文中，取出add函数
    2. 对add函数的这段代码进行编译，并创建**该函数的执行上下文**和**可执行代码**
    3. 最后执行代码

    多个执行上下文**通过一种叫做栈的数据结构来管理**

  - JavaScript的调用栈： 在执行上下文创建好后，JavaScript引擎会将执行上下文压入栈中，通常把这种用来管理执行上下文的栈称为执行上下文栈，又称**调用栈**

  
  **调用栈总结**
  
  1. 每调用一个函数，JavaScript引擎会为其创建执行上下文，并把该执行上下文压入调用栈，然后JavaScript引擎开始执行函数代码
  2. 如果在一个函数A中调用了另外一个函数B，那么Javascript引擎会为B函数创建执行上下文，并将B函数的执行上下文压入栈顶。
  3. 当前函数执行完毕后，JavaScript引擎会将该函数的执行上下文弹出栈
  4. 当分配的调用栈空间被占满时，会引发”堆栈溢出“问题

### 块级作用域 let和const

**作用域是指在程序中定义变量的区域，该位置决定了变量的生命周期。通俗地理解，作用域就是变量和函数的可访问范围，即作用域控制着变量和函数的可见性和生命周期**

块级作用域就是`{}`包裹的一段代码，比如函数

**变量值的查找路径：沿着词法环境的栈顶向下查询，如果在词法环境中的某个块中查找到了，就直接返回给JavaScript引擎，如果没有查找到，那么继续在环境变量中查找。**

由于js的变量提升存在着变量覆盖，变量污染等设计缺陷，所以ES6引入块级作用域关键字来解决这些问题

````javascript
let myname = 'hello'
{
	console.log(myname)
	let myname='world'
}
//referenceError
````

原因：在块作用域内，let声明的变量被提升，但变量只是创建被提升，初始化并没有被提升，在初始化之前使用变量，就会存在一个暂时性死区

- **var**的创建和初始化会被提升，赋值不会被提升
- **let**的创建会被提升，初始化和赋值不会被提升
- **function**的创建，初始化和赋值都会被提升

ps: const 和 let都是放在词法环境汇总滴

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

### 作用域链和闭包

在js中，实现外部作用域访问内部作用域中变量的方法叫做闭包

### 八种方式实现继承

1. 原型链方式

过多的继承了没用的属性

   ````javascript
 //原型链继承
function Grand(){}
Grand.prototype.name = 'fathger'  
const grand = new Grand()
function Father(){
    this.age = 15
}
Father.prototype = grand
const father = new Father()
console.log(father.name, father.age, father)
   ````

2. 借用构造函数继承（类式继承）

有点：可以传参

缺点：不能继承借用构造函数的原型

​           每次构造函数都要多走一个函数

````javascript
function Student(name, age){
    this.name = name
    this.age = age
}
function Teacher(name, age, id){
    Student.call(this, name, age)
    this.id = id
}
const teacher = new Teacher('wa', 18, '100')
console.log(teacher)
````

3. 组合式继承(通俗来讲就是用原型链实现对原型属性和方法的继承 用借用构造函数来实现对实例属性的继承)

优点： 避免了原型链和构造函数的缺点，在js中比较常用

缺点： 实例和原型上存在两份相同的属性

````javascript
function Parent(){
	this.name = 'parent'
}

Parent.prototype.sayName = function(){
	console.log(this.name)
}

function Child(){
    this.sayHello = 'hello'
    Parent.apply(this, [])
}
Child.prototype = new Parent()
Child.prototype.constructor = Child
let child  = new Child()
console.log(child, child.sayName)
````

### 说说js的垃圾回收(GC)

