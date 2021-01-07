# 手写js实现

## 数组相关

### 数组扁平化

数组扁平化是指将一个多维数组变成为一个一维数组

````javascript
const arr = [1,[2,[3,[4,5]]],6]
//[1,2,3,4,5,6]
````

- 使用`flat` ('deep') //深度

  ````
  arr.flat(Infinity)
  ````

- join, split

  ```
  arr.join(',').split(',')
  ```

- 正则

  ```
  let arrStr = JSON.stringify(arr)
  arrStr.replace(/\[|\]/g, '').split(',')
  ```

- 递归

  ```
  let cloneArray = []
  function flatFn(arr){
      if(!Array.isArray(arr)){
          cloneArray.push(arr)
       }else{
           for (const arrItem of arr) {
              flatFn(arrItem)
            }
        }
  }
  ```

- reduce

  ```
   const flatten = arr => {
              debugger
              console.log(arr)
              return arr.reduce((sum, cur) =>{
                  return sum.concat(Array.isArray(cur) ? flatten(cur) : cur)
              }, [])
          }
  ```

 ### 数组去重

```
const arr = [1, 1, '1', 17, true, true, false, false, 'true', 'a', {}, {}];
// => [1, '1', 17, true, false, 'true', 'a', {}, {}]
```

- 通过set保存的值总是唯一

  `Array.from`将类数组对象或者可遍历的对象转为数组

  ```
  Array.from(new Set(arr))
  ```

- 两层for循环+splice, 时间复杂度为n*n

- **利用对象的唯一属性** 使用**Map**

  Map不同类型都可以作为`key`

  ````javascript
  cosnt unique3 = arr => {
  	let map = new Map()
  	const res = []
  	arr.forEach((item, index) => {
  		if(!map.has(arr[index])){
  			res.push(arr[index])
  			res.set(arr[index], true)
  		}
  	})
  }
  ````

- filter

  ```javascript
  const unique4 = arr => {
              return arr.filter((item, index) => {
                  return arr.indexOf(item) === index;
              });
          }
  ```

### 类数组转为数组

​	类数组具有`length`属性，但不具有数组原型上的方法。常见的类数组有arguments,Dom操作返回的的结果

- `Array.from(document.querySelectorAll('div'))`

- `Array.slice.call(arguments)`

- 使用扩展运算符

  `[...arguments]`

- `Array.prototype.concat.apply([], arguments)`

### `Array.prototype.filter()`

filter()方法创建一个新的数组，新数组中的元素是通过检查指定数组中符合条件的所有元素。

filter()不会对空数组进行检测。filter()不会改变原始数组。

 ```javascript
array.filter(function(currentValue, index, arrObj), thisValue) 
//arrObj 是数组对象
//在回调函数中thisValue用作this的值， 省略thisValue, this的值为undefined
 ```

```javascript
Array.prototype.filterArr = function(callback, thisArg){
            if(typeof callback !== 'function'){
                throw new Error('callback is must be a function')
            }
            //让obj作为对象传递，强制转为array对象
            const res = []
            let obj = Object(this)
            // >>> 0 保证 len为number 并且为正整数， x >>> 0本质上就是保证x有意义（为数字类型），且为正整数，
            let len = obj.length >>> 0
            for(let i = 0; i < len; i++){
                debugger
                if(callback.call(thisArg, obj[i], i, obj)){
                    res.push(obj[i])
                }
            }
            return res 
        }
```

### `Array.protytoye.map()`

```
array.map(function(currentValue,index,arr), thisValue)
```

```javascript
Array.prototype.mapArr = function(callback, thisArg){
            debugger
            if(typeof callback !== 'function'){
                throw new Error('callback is must be a function')
            }
            const res = []
            const obj = Object(this)
            const len = obj.length >>> 0
            for(let i = 0; i < len; i++){
                res.push(callback.call(thisArg, obj[i], i, obj))
            }
            return res
        }
```



### `Array.prototype.reduce()`

````javascript
Array.prototype.reduce(function(accumulator, curVal, index, array){}, initialValue)
````

```javascript
Array.prototype.reduceArr = function(callback, initialVal){
	if(typeof callback !== 'function'){
		throw new Error('callback must be a function')
	}
    const obj = Object(this)
    const len = obj.length >>> 0
    if(len <= 0){
        return 
    }
    let accuVal = ''
    let i = 0
    if(initialVal === undefined){
        i = 1
        accuVal = obj[0]
    }else{
        accuVal = initialVal
    }
    for(i; i < len; i++){
        accuVal = callback(accuVal, obj[i], i, obj)
    }
    return accuVal
}
```

### `Array.prototype.forEach()`

```javascript
Array.prototype.forEachArr = function(callback, thisArg){
            if(typeof callback !== 'functon'){
                throw new Error('callback must be a function')
            }
            const o = Object(this)
            const len = o.length >>> 0
            for(let i = 0; i < len; i++){
                callback.call(thisArg, o[i], i, o)
            }
        }
```



## `apply` `call` `bind`

- call

```javascript
Function.prototype.call = function(context = window, ...args){
    //没有传入对象则绑定到window上
   if(typeof this !== 'function'){
        throw new TypeError('type error')
    }
    const fn = Symbol('fn')
    context[fn] = this //把函数赋值到对象的某个属性
    const res = context[fn](...args)
    delete context[fn]
    return res
}
```

- apply

```javascript
Function.prototype.apply = function(context = window, args){
	//没有传入对象则绑定到window上
	if(typeof this !== 'function'){
       throw new TypeError('type error')
    }
    const fn = Symbol(this)
    context[fn] = this 
    const res = context[fn](...args) 
    delete context[fn]
    return res
}
```

- bind

````javascript
Function.prototype.bind = function(context,  ...args){
    if(typeof this !== 'function'){
       throw new Error('type error')
    }
    //保存this的值
    let self = this
    return function F(){
        //考虑new的情况
        if(this instanceof F){
            return new self(...args, ...arguments)
        }
        
        return self.apply(context, [...args, ...arguments])
    }
}
````



## 考察性能意识

### 防抖函数

使用场景

- 关键字远程搜索下拉框
- resize

````javascript
function debounce(fn, delay){
	let timer = null
	return function(){
		timer && clearTimeout(timer)
		fn.apply(this, arguments)
	}
}
````

### 节流函数

使用场景

- 滑动滚动条
- 射击类游戏发射子弹
- 水龙头的水流速

对于某些连续的事件，为了表现平滑过渡，这中间的状态我们也需要关心的，但减弱密集型事件的频率依旧是性能优化的杀器

````javascript
function throttle(fn, delay = 100){
	let timer, lastTime
	return function(){
		const now = Date.now()
		const space = lastTime ? now - lastTime : 0
		//不是首次
		if(lastTime && space < delay){
			timer && clearTimeout(timer)
			setTimeout(()={
                lastTime = Date.now() //重新计时
				fn.apply(this, arguments)
			}, delay - space)
            return
		}
    	//首次执行
    	lastTime = now
    	fn.apply(this, arguments)
	}
}
````

### 事件代理

````javascript
function delegate(ele, selector, type, fn){
	function cb(e){
        e = e || window.event
        let target = e.target || e.srcElement //ie
        let selectors = document.querySelectorAll(selector)
        selectors = [].slice.call(selectors)
        if(selectors.includes(target)){
           fn.call(target, e)
        }
    }
    ele.removeEventListener(type, cb, false)
    ele.addEventListener(type, cb, false)
}
````

### 请求并发限制

题目： 请实现如下函数，可以批量请求数据，所有url地址在urls参数中，同时可以通过max参数控制请求的并发数，当所有请求结束之后，需要执行callback回调函数。发送请求的函数使用fetch即可

````
function sendRequest(urls: string[], max: number, callback: ()=>void){}
````

````javascript
function sendRequest(urls, max, callback){
	if(max <= 0){
		return throw new Error('sendRequest limit is not less 0')
	}
	let len = urls.length
	let curSendCnt = Math.min(len, max) //当前需要执行的数
	let tasks = []
	let resList = []
    let cnt = 0 //累计执行数
	tasks = urls.map((url, index) => fetch(url).then(res => {
		resList[index] = res
	}).catch(err => {
        resList[index] = err
    }).finally(() => {
        if(cnt === len){
           callback(resList)
            return 
        }
        ++curSendCnt
        doTasks()
    }))
    function doTasks(){
        while(curSendCnt > 0){
           --curSendCnt
            let task = tasks.shift()
            task()
        }
    }
	
}
````

## 考察异步请求问题

### JSONP

script标签不遵循同源协议，可以用来进行**跨域请求** ，优点：兼容性好，缺点：只能get请求

```javascript
function jsonp(url, callbackName, params = {}){
	function genUrl(){
		let parStr = ''
		for(let key in params){
			if(Object.hasOwnProperty.call(params, key)){
				parStr += `&${key}=${params[key]}`
			}
		}
		if(url.indexOf('?') === -1){
			parStr = parStr.slice()
			url = url + '?' + parStr
		}else{
			url = url + parStr
		}
		return url
	}
	
	return new Promise((resolve, reject) => {
		let jsEle = document.createElement('script')
		jsEle.setAttribute('src', url)
		document.body.appendChild(jsEle)
		window[callbackName] = (data) => {
			resolve(data)
			document.body.removeChild(scriptEle)
		}
	})
}
```

### AJAX

```javascript
function axios(url, method='GET', data = {}){
    return new Promise((resolve, reject) => {
        const xhr = new XMLHttpRequest()
        chr.open(method.toUpperCase(), url, true)
        xhr.onreadystatechange= ()=>{
            if(xhr.readyState !== 4){
                return
            }
            
            if(xhr.status == 200){
               let res = xhr.response || xhr.responseText
               resolve(res)
            }else{
                reject(new Error('some error'))
            }
        }
        xhr.onerror = function handleError(){
            //do something
            reject(new Error('network error'))
        }
        xhr.send(data)
    })
}
```

## `promise`

promise使用了**回调函数的延时绑定**，**回调函数返回值穿透**， **错误冒泡**技术

- **Promise 中为什么要引入微任务？**

  promise采用`.then`绑定回调函数，而new promise的时候会立刻执行promise中的函数，所以就会产生一个问题：先执行后绑定函数，因此采用了定时器推迟绑定回调函数的使用，定时器的效率并不是太高，因此Promise又把这个定时器改造成了微任务。

- **Promise中是如何实现回调函数返回值穿透的？**

  为什么可以链式调用，因为Promise.then返回的是一个Promise对象。将这个新的Promise实例的返回值传递给下一个then中。将返回值继续放到`resolve`参数中

- **Promise出错后，是怎么通过冒泡传递给最后那个捕获异常的函数？**

  promise中有成功和失败的回调函数

### 手动实现一个promise

```javascript
const PENDING = 'PENDING'
        const REJECTED = 'REJECTED'
        const FULFILLED = 'FULFILLED'
        class Bromise {
            constructor(executor){
                this.status = PENDING
                this.value = undefined
                this.err = undefined
                this.onFulfilledCallbacks = []
                this.onRejectedCallbacks = []
                const resolve = (val) => {
                    if(this.status === PENDING){
                        this.value = val 
                        this.status = FULFILLED
                        this.onFulfilledCallbacks.forEach(cb => {
                            cb(val)
                        })
                    }
                }
                const reject = (err) => {
                    if(this.status === PENDING){
                        this.err = err 
                        this.status = REJECTED
                        this.onRejectedCallbacks.forEach(cb => {
                            cb(err)
                        })
                    }
                }
                executor(resolve, reject)
            }

            then(onFulfilled, onRejected){
                onFulfilled = typeof onFulfilled === 'function' ?
                    onFulfilled : value => value
                
                onRejected = typeof onRejected === 'function' ? 
                    onRejected : 
                (err) => {throw new Error(err instanceof Error ? Error.message: errll)}
                const that = this 
                return new Bromise((resolve, reject) => {
                    if(that.status === PENDING){
                        that.onFulfilledCallbacks.push(()=>{
                            try {
                                setTimeout(()=>{
                                    let result = onFulfilled(that.value)
                                    //1. 回调函数返回值是Bromise, 执行then操作
                                    //2. 不是回调函数则执行 resolve
                                    result instanceof Bromise ? 
                                        result.then(resolve, reject) : resolve(result)
                                })
                            } catch (error) {
                                reject(error)
                            }
                        })
            
                        that.onRejectedCallbacks.push(()=>{
                            try {
                                setTimeout(()=>{
                                    let result = onRejected(that.err)
                                    result instanceof Bromise ? 
                                        result.then(resolve, reject) : resolve(result)
                                })
                            } catch (error) {
                                reject(error)
                            }
                        })
                    }else if(that.status === FULFILLED){
                        try {
                            setTimeout(()=>{
                                let result = onFulfilled(that.value)
                                result instanceof Bromise ? 
                                    result.then(resolve, reject) : resolve(result)
                            })
                        } catch (error) {
                            reject(error)
                        }
                    }else if(that.status === REJECTED){
                        try {
                            setTimeout(()=>{
                                let result = onRejected(that.err)
                                result instanceof Bromise ? 
                                    result.then(resolve, reject) : resolve(result)
                            })
                        } catch (error) {
                            reject(error)
                        }
                    }
                })
            }
            
            catch(onRejected){
                return this.then(null, onRejected)
            }

            //1. value 是 Promise 2. value不是Promise
            static resolve(value){
                if(value instanceof Bromise){
                    return value
                }else{
                    //创建一个实例，status 为fulfilled
                    return new Bromise((resolve, reject) => resolve(value))
                }
            }

            static reject(err){
                return new Bromise((resolve, reject) => reject(err))
            }
            static race(promiseArr){
                return new Bromise((resolve, reject) => {
                    promiseArr.forEach(promise => {
                        Bromise.resolve(promise).then(val => {
                            resolve(val)
                        }, err => {
                            reject(val)
                        })
                    })
                })
            }
            static all(promiseArr){
                let cnt = 0
                let values = []
                return new Bromise((resolve, reject) => {
                    for(let i = 0; i < promiseArr.length; i++){
                        Bromise.resolve(promiseArr[i]).then(val => {
                            values[i] = val 
                            ++cnt
                            if(cnt === promiseArr.length){
                                resolve(val)
                            }
                        }, err => {
                            reject(val)
                        })
                    }
                    
                    promiseArr.forEach(promise => {
                        Bromise.resolve(promise).then(val => {
                            resolve(val)
                        }, err => {
                            reject(val)
                        })
                    })
                })
            }
        }
```

### `promise.all`

### `promise.race`

### promise并行限制

JS实现一个带并发限制的异步调度器Scheduler, 保证同时运行的任务最多两个。请完善下面的Scheduler类，

使以下程序能够正常输出

````javascript
class Scheduler {
  add(promiseCreator) { ... }
  // ...
}
   
const timeout = time => new Promise(resolve => {
  setTimeout(resolve, time);
})
  
const scheduler = new Scheduler();
  
const addTask = (time,order) => {
  scheduler.add(() => timeout(time).then(()=>console.log(order)))
}

addTask(1000, '1');
addTask(500, '2');
addTask(300, '3');
addTask(400, '4');
// output: 2 3 1 4
````

整个的完整执行流程：

1. 其实1、2两个任务开始执行
2. 500ms时，2任务执行完毕，输出2，任务3开始执行
3. 800ms时，3任务执行完毕，输出3，任务4开始执行
4. 1000ms时，1任务执行完毕，输出1，此时只剩下4任务在执行
5. 1200ms时，4任务执行完毕，输出4

```javascript
class Scheduler {
    constructor(){
        this.curCnt = 0 //当前正在执行任务的数量
        this.limit = 2 //并发限制数
        this.taskList = [] //任务列表
    }
    add(promiseCreator) { 
        if(!promiseCreator){
            return 
        }
        this.taskList.push(promiseCreator)
    }
    startTask(){
        this.doTasks()
    }
    //执行任务
    doTasks(){
        //任务队列中有任务，并且没有超出限制，则执行
        while(this.curCnt < this.limit && this.taskList.length > 0){
            let curTask = this.taskList.shift() //当前任务
            ++this.curCnt
            curTask().then(()=>{
                 --this.curCnt
                if(this.taskList.length > 0){
                    this.doTasks()
                }
            }).catch(err=>{
                --this.curCnt
                if(this.taskList.length > 0){
                    this.doTasks()
                }
            })
        }
    }
}
```

## 函数&&对象

### 函数柯里化

指的是一个接受多个参数的函数变为接受一个参数返回一个函数的固定形式，这样便于再次调用, 例如 f(1)(2)

经典面试题：实现`add(1)(2)(3)(4) = 10` `add(1,2,3)(2)=9`

````
function add(...args){
    let _args = args
    function fn(){
        _args.push(...arguments)
        return fn 
    }
    fn.toString = function(){
        return _args.reduce((sum, cur) => {
        return sum + cur
    })
    }
    return fn  
}
````

### 模拟new操作

````javascript
function Student(name,age){
    this.name=name
    this.age=age
}

let first=new Student('dylan','26')
console.log(first.name);// dylan
console.log(first.age);// 26
````

new 实现的功能：

1. 创建一个空对象
2. 空对象的原型  `__proto__`  指向构造函数的 原型 `prototype`
3. 让this指向新创建的空对象，并且执行对象的主体(为这个新对象添加属性)
4. 判断返回值的类型，如果是值类型就返回创建的对象，如果是引用类型，就返回这个引用类型的对象
5. 如果函数没有返回对象类型Object(包括Function，Array, Date, RegExg, Error), 那么new表达式中的函数调用将返回该对象的引用

```javascript
function newOperator(ctor, ...args){
    if(typeof ctor !== 'function'){
       throw new TypeError('Type Error')
    }
    const obj = Object.create(ctor.prototype)
    const res = ctor.apply(obj, args)
    const isObject = typeof res === 'object' && res !== null
    const isFunction = typeof res === 'function'
    return isObject || isFunction ? res : obj
}
```



### `instanceof`

`instanceof`运算符用于检测构造函数的`prototype`属性是否出现在某个实例对象的原型链上

```javascript
function myInstanceof(instance, origin){
            //基本类型全部返回false
            if(typeof instance !== 'object' || instance === null){
                return false
            }
            let proto = Object.getPrototypeOf(instance)
            while(true){
                if(proto === null){
                    return false
                }
                if(proto === origin.prototype){
                    return true 
                }
                proto = Object.getPrototypeOf(proto)
            }
        }
```



### 原型继承

寄生组合继承

```javascript
function Parent(){
	this.name = 'parent'
}
function Child() {
	Parent.call(this)
	this.type = 'children'
}
Child.prototype = Object.create(Parent.prototype)
Child.prototype.constructor = Child
```



### `Object.is`

解决一下问题

````
+0 === -0 // true
NaN === NaN //false
````

````javascript
function is = (x, y) => {
	if(x === y){
		return x !== 0 || y !== 0 || 1/x !== 1/y
	}else{
		return x !== x && y !== y
	}
}
````

### `Object.assign`

`Object.assign()`方法用于将所有可枚举属性的值从一个或多个源对象复制到目标对象。它将返回目标对象 (浅拷贝)

```javascript
Object.defineProperty(Object, 'assign', {
    value: function(target, ...args){
        if(target === null){
           return new TypeError('Cannot convert undefined or null to object')
        }
        //目标对象需要统一是引用数据类型， 若不是，会自动转换
        const to = Object(target)
        
        for (let i = 0; i < args.length; i++) {
            //每一个源对象
            const nextSource = args[i]
            if(nextSource !== null){
               for(const nextKey in nextSource){
                   if(Object.hasOwnProperty.call(nextSource, nextKey)){
                      to[nextKey] = nextSource[nextKey]
                   }
               }
            }
        }
        return to
    }
})
```



### 深拷贝

````javascript
function deepClone(target){
    if(typeof target !== 'object' || target === null){
        return target 
	}
    let clone = Array.isArray(target) ? [] : {}
    for(let key in target){
        if(Object.hasOwnProperty.call(target, key)){
           clone[key] = target[key]
         }
    }
    return clone
}
````



## 考察设计模式

### 单例模式

### 发布订阅模式

### 工厂模式

## 一些常用的功能函数

### 数字格式化(三个中间加"，")

````javascript
function formatNumber(a:number){
    const list = a.toString().split('.')
    let x1 = list[0]
    let x2 = list[1] ? '.' + list[1] : ''
    const reg = /(\d+)(\d{3})/
    while(reg.test(x1)){
        x1 = x1.replace(reg, '$1' + ',' + '$2')
    }
    return x1 + x2
}
````



### 数字递增动画`window.requestAnimationFrame()`







