# vue

## 基本知识

## 高级特性

### 自定义事件，自定义组件V-model

* 不同于组件和prop,自定义事件名称需要完全匹配（不存在任何的大小写转化），组件自定义事件名使用`kebab-base` 而不是`kebabBase`

* 自定义组件的`v-model`

  父组件`<my-input v-model="testVal"/>`

  子组件

  ```
  <template>
  	<div>
  	  <input :value="testVal" @input="inputValHandler"/>
  	</div>
  </template>
  <script>
  	export{
  		model: {
  		   prop: 'inputed',
              event: 'changed'
  		},
  		props: ['inputed'],
          mathods: {
              inputValHandler(e){
                  thie.$emit('changed', e.target.value)
              }
          }
          
  	}
  </script>
  ```


### 动态,异步组件

​	动态组件

```
() => import('./..')
```



### slot

- 插槽的编译作用域

  父组件是不能直接访问子组件中的内容滴

   **父模板里的所有内容都是在父级作用域中编译的， 子模板里的所有内容都是在子作用域中编译的**

  ````
  <navigation-link url="/profile">
    Clicking here will send you to: {{ url }}
    <!--
    这里的 `url` 会是 undefined，因为其 (指该插槽的) 内容是
    _传递给_ <navigation-link> 的而不是
    在 <navigation-link> 组件*内部*定义的。
    -->
  </navigation-link>
  
  ````

- 后备内容

  可以在slot中添加一些默认的内容，如果父元素没有使用，则是该默认的

- 具名插槽

  出现在多个slot中， 一个不带name的`<slot>`出口会带有隐含的名字default

  `v-slot`只能添加再`<template>`上（除了独占默认插槽）

  ```
  //child
  <div>
          <header>
              <slot name="header"></slot>
          </header>
          <footer>
              <slot name="footer"></slot>
          </footer>
      </div>
  //parent
  <music-list>
      <template v-slot:footer>
      <div>我是尾部</div>
      </template>
      <template v-slot:header>
      <div>我是头部</div>
      </template>
  </music-list>
  ```

- 作用域插槽

  为了能够访问子组件的内容， 可以将子组件中slot标签上绑定一个属性，绑定在slot元素上的属性成为**插槽prop** ,可以在父元素上

  ````
  //child
  <div>
      <header>
      <slot name="header" :head-obj="firstName"></slot>
      </header>
      <footer>
      <slot name="footer" :foot-obj="lastName"></slot>
      </footer>
  </div>
   data(){
     return {
         firstName: 'first name',
         lastName: 'last name'
     }
    }
    
  //parent
  <music-list>
        <template v-slot:footer="foName">
          <div>
            {{foName.headObj.firstName}}
            我是尾部
          </div>
        </template>
        <template v-slot:header="heName">
          <div>
            {{heName.footObj.lastName}}
            我是头部
          </div>
        </template>
      </music-list>
  ````

- 独个插槽使用

  ````
  <music-list v-solot="obj">
   </music-list>
  ````

  

- 解构插槽`prop`

  可以使用es6语法，

  ````
  //parent
  <music-list>
        <template v-slot:footer="{headObj}">
          <div>
            {{headObj.firstName}}
            我是尾部
          </div>
        </template>
        <template v-slot:header="heName">
          <div>
            {{heName.footObj.lastName}}
            我是头部
          </div>
        </template>
      </music-list>
  ````

- 动态插槽的插槽名 `<template v-slot:[dynamicSlotName]>...</template>`

- `v-slot:`缩写为`#` eg: `v-slot:header` 可以被重写为`#header`

- 

### $nextTick

​	在下一次渲染更新之后调用

### keep-alive

​	保持存活

### mixin

​	代码复用

1. 缺点 代码引用不明确，不利于维护
2. 方法名，变量名容易冲突

### 自定义指令

除了`v-model` `v-show`等内置指令外， vue也可以注册自定义指令。一般用于对普通DOM元素进行底层操作， 如： 自动获取输入框的焦点(autofocus 在移动版safari不工作)，可以自定义指令实现

````
//全局注册
Vue.directive('focus', {
 //当被绑定的元素插入到DOM中时
 inserted: function(el){
 	//聚焦元素
 	el.focus()
 }
})
//也可以局部注册
directives: {
  focus: {
  	//指令的定义
  	inserted: function(el){
  		el.focus()
  	}
  }
}
//使用
<input v-focus>
````

**钩子函数**

- bind: 只掉用一次，指令第一次绑定到元素时调用。在这里可以进行一次性的初始化设置
- inserted: 被绑定元素插入父节点时调用（仅保证父节点存在，但不一定已被插入文档中）
- update: 所在组件的VNode更新时调用，看可能发生在其子VNode更新之前。指令的值可能发生改变，也可能没有。是你可以通过比较更新前后的值来忽略不必要的模板更新
- componentUpdated: 指令所在组件的 VNode **及其子 VNode** 全部更新后调用
- unbind: 只调用一次，指令与元素解绑时调用

指令函数能够接受所有合法的 JavaScript 表达式。

## 复习的知识点

### MVVM

### 声明周期

### 数据绑定

### 状态管理

### 组件通信

### computed/watch 原理

### Virtual Dom

### diff

## 面试题

[面试题1](https://juejin.cn/post/6844903918753808398)

[面试题2](https://juejin.cn/post/6844904084374290446)

[综合](https://juejin.cn/post/6844903928442667015)

### 陌生的一些题

#### 直接给一个数组项赋值，vue能检测到吗

**如果在实例创建之后添加新的属性到实例上，它不会触发视图更新** 

由于js的限制， vue不能检测到以下数组的变动

- 当你利用索引直接设置一个数组项时， 例如`vm.items[indexOfItem] = newVal`
- 当你修改数组长度时，例如`vm.items.length = newLength`

为了解决第一个问题， vue提供一下操作

````
Vue.set(vm.items, indexOfItem, newValue)
Vue.$set(vm.items, indexOfItem, newValue)//Vue.set的别名
vm.items.splice(indexOfItem, 1, newValue)
````

为了解决第二个问题，vue提供了一下操作

````
vm.items.splice(newLength)
````

#### vue的生命周期

1. 生命周期是什么

   vue实例有一个完整的生命周期，也就是从开始创建、初始化数据、编译模板、挂在DOM->渲染、更新->渲染、卸载等一系列过程，我们称这就是vue的生命周期

2. 各个生命周期的作用

   | 生命周期      | 描述                                                         |
   | ------------- | ------------------------------------------------------------ |
   | beforeCreate  | 组件实例被创建之前调用，组件的属性生效之前                   |
   | created       | 组件实例创建完成后被调用，属性也已经被绑定，但真实的dom还没有生成，$el还不可用 |
   | beforeMount   | 组件挂载之前调用，相关的render函数首次调用                   |
   | mounted       | el被创建的vm.$el替换， 并挂在到实例上去之后调用该钩子        |
   | beforeUpdate  | 组件数据更新之前调用，发生在虚拟DOM打补丁之前，              |
   | updated       | 组件数据更新之后                                             |
   | activited     | keep-alive专属，组件被激活时候调用                           |
   | deactivited   | keep-alive专属，组件被销毁时候调用                           |
   | beforeDestory | 组件古北销毁前调用（可以初始化一些数据，防止内存泄漏）       |
   | destoryed     | 组件销毁时调用                                               |

3. ![1.png](vue.assets/16ca74f183827f46)



#### vue父子组件生命周期钩子执行顺序

- 加载渲染过程

  ````
  父-beforeCreate -> 父-created -> 父-beforeMount -> 子-beforeCreate -> 子-created -> 子-beforeMount -> 子-mounted -> 父-mounted
  ````

- 父组件更新

  ````
  父-beforeUpdate -> 父-updated
  ````

- 子组件更新

  ````
  父-beforeUpdate -> 子-beforeUpdate -> 子-updated -> 父-updated
  ````

- 销毁过程

  `````
  父-beforeDestory -> 子-beforeDestory -> 子destoryed -> 父destoryed
  `````

#### 父组件监听子组件的生命周期

如果想在父组件想在子组件的mounted做点事情

```
//父组件
<child @mounted="doSomthing"></child>
methods: {
    doSomthing(){}
}

//子组件
mounted(){
    this.$emit('mounted')
}
```

使用`hook` 省去子组件的emit

```
//父组件
<child @hook:mounted="doSomthing"></child>
methods: {
    doSomthing(){}
}
```

在任意地方使用`Vue.$on/$once('hook:生命周期'， callback)`可以监听生命周期

#### vue-router的路由模式有几种？

- history
- hash
- abstract 在js运行环境中转为abstract

#### vue是如何实现双向绑定的

以下解释不是最新的,   `proxy`  `Object.defineProperty`

vue 内部通过`Object.defineProperty` 方法属性拦截方式，把data对象里的每个数据的读写转化成`getter/setter` , 当数据变化时通知更新视图。

vue中通过以下4个步奏实现

- `Observer`监听器，用来劫持并监听所有属性，如果发生变化，就通知所有订阅者
- `Dep`实现观察-订阅模式，用来收集订阅者，当数据变化时，执行订阅者更新函数。对监听器`observer`和订阅者`watcher`进行统一管理
- `Watcher`实现一个订阅值，可以收到属性变化的通知，并执行相应的方法，从而更新视图
- `Compile` 解析template模板上面每个节点的指令，对模板数据和订阅去进行初始化

#### VUE的proxy

**Proxy**对象用于创建一个对象的代理，从而实现基本操作的拦截和自定义(如属性查找、赋值、枚举、函数调用等)

**语法** `const p = new Proxy(target, handler)`

- target: 要使用`proxy`包装的目标对象（可以是任何类型的对象，包括原生数组、函数，甚至另一个代理）
- handler: 一个通常以函数作为属性的对象，各属性中的函数分别定义了在执行各种操作时代理`p`的行为

`Proxy.revocable()` 创建一个可撤销的`Proxy`对象

#### diff常考问题

​	[一个答案的小链接](http://www.360doc.com/content/20/0609/09/13328254_917329351.shtml)

​	感觉不太准确

- 什么时候用到diff算法，diff算法的作用域在哪里？

  ps：diff算法并非vue专用，其他的一些框架都在用，凡事涉及虚拟dom的多半都要用到diff算法。更新视图-进行新老节点比较

  diff的策略：深度有限，同层比较

  patchChildren根据是否存在key进行真正的diff或者直接patch

  既然diff算法存在patchChildren方法中，而patchChildren方法用在fragment类型和element类型的vnode中。

- diff算法是怎么运作的，到底有什么作用？

  diff作用就是在patch子vnode过程中，找到与新vnode对应的老vnode，复用真实的dom节点，避免不必要的性能开销。

  对于不存在key情况下，

  1. 比较新老children的length获取最小值，然后对于公共部分，进行重新patch
  2. 如果老节点数量大于新结点数量，移除多出来的节点
  3. 如果新的节点数量大于老节点的数量，重新mountChildren新增的节点

  对于存在key的情况，会用到diff算法

  大致流程：

  1. 从头对比找到有相同的节点patch，发现不同，立即跳出

  2. 如果第一步没有patch完，从后向前开始patch，如果发现不同，立即跳出循环

  3. 如果新的节点大于老的节点，对于剩下的节点全部以新的vnode处理(这种情况说明已经patch玩相同的vnode)

  4. 对于老的节点大于新的节点情况，对于超出的节点全部卸载（这种情况说明已经patch完相同的vnode）

  5. 不确定的元素（这种情况说明没有patch完相同的vnode）,与3,4对立关系

     - 把没有比较过的新的vnode节点，通过map保存

       记录已经patch的新节点的数量patched

       没有经过patch新的节点的数量toBePatched

       简历一个数组newIndexToOldIndexMap, 每个子元素都是[0,0,0,0],里面的数字记录老节点的索引，数组索引就是新结点的索引

       **开始遍历老节点**

       - 如果toBePatched新的节点数量为0，那么统一卸载老的节点

       - 如果老节点的key存在，通过key找到对应的index

       - 如果，老节点的key不存在

         遍历剩下的所有新节点

         如果找到与老节点对应的新节点，将新结点的索引赋值给newIndex

       - 没有找到与老节点对应的新结点，卸载当前老节点

       - 找到与老节点对应的新结点，把老节点的索引，记录在存放新结点的数组中，

         如果节点发生移动，记录已经移动了

         patch新老节点，找到新的节点进行patch节点

         **遍历结束**

       **遍历结束**

       **如果发生移动**

       1. 根据newIndexToOldIndexMap 新老节点索引列表找到最长稳定序列
       2. 对于newIndexToOldIndexMap - item = 0 证明不存在老节点，从新行程新的vnode
       3. 对于发生移动的节点进行移动处理

- 在v-for循环列表key的作用是什么

  通过判断newVnode和oldVnode的key是否相等，从而复用与新结点对应的老节点，节约性能的开销

- 用索引index做key真的有用？ 到底用什么做key才是最佳方案

  在diff算法中， 通过isSameVNodeType方法判断，来判断key是否相等，判断当前节点是否相等

  用index做key的效果实际和没有用diff算法是一样的，当我们用index作为key时候，无论我们怎么移动或者移除节点，到了diff算法中都会从头到尾一次patch 重新创建

  用index拼接其他值作为索引

  当已用index拼接其他值作为索引的时候，因为每一个节点都找不到对应的key，所有节点都不能复用，都需要重新创建，

  **正确用法**

  使用唯一值id做key， 可以通过前后端交互的数据源的id为key

#### $nextTick实现原理

在下次DOM更新循环结束之后执行延时回调。在修改数据之后立即使用这个方法，获取更新后的DOM。

vue实现响应式**并不是数据发生变化之后DOM立即变化**，而是按照一定的策略进行DOM更新

vue在修改数据后，视图不会立刻更新，而是等同一事件中的所有数据变化完成之后，再统一进行视图更新。

该函数传入一个cb，这个cb会被存储到一个队列中，在下一次tick时触发队列中的所有cb事件。

vue事件循环：

​	**第一个tick**

1. 首先修改数据，这是同步任务。同一事件循环的所有同步任务都在主线程上执行，行程一个执行栈，此时还未涉及DOM，
2. 在watcher的update方法中调用了`queuewatcher`方法，该方法过滤掉了重复的watcher,将最新的watcher，放到其中。调用了`nextTick`方法，nextTIck方法中往异步队列中push该事件。

**第二个tick**

​	同步任务执行完毕，开始执行异步watcher队列的任务，更新DOM， vue内部使用Promise,MutationObserver, setImmediate setTimeout(fn, 0)（以上都不支持，则用setTimeout） 实现

**在改变data值之后调用$nextTick方法可以获取最新dom** 但是如果在  **在改变data值之前调用$nextTick方法不能可以获取最新dom** 他在异步dom更新队列之前

**MutationObserver** ,创建并返回一个新的`MutationObserver`它会在指定的DOM发生变化时被调用





**eventloop**时间循环机制：

- 同步和异步任务分别进入不同的执行“场所”， 同步的进入主线程，异步的进入Event Table并注册函数
- 当指定的事情完成时，Event Table会将这个函数移入Event Queue
- 主线程的任务执行完毕后，会去Event Queue读取对应的函数，进入主线程执行
- 上述过程会不断重复，也就是常说的事件循环机制

如何知道主线程执行栈为空？ js引擎存在 monitoring process进程， 会持续不断检查主线程执行栈是否为空，一旦为空，就会去Event Queue检查是否有等待被调用的函数

宏任务：script(主代码块)、 setTimeout setInterval setImmediate i/o ui rendering

微任务：Promise Process.nextTick（nodejs） Object.observe  MutationObserver

#### VUEX原理

- 原理

  Vuex本质是一个对象

  vuex对象有两个属性，一个是install方法， 一个是Store这个类

  install方法的作用是将store这个实例挂载到所有的组件上，注意是同一个store实例

  Store这个类拥有commit, dispatch这些方法，Store类里将用户传入的state包装成data， 作为new Vue的参数， 从而实现state值的响应式

  ````
  let Vue = null 
  
  class Store {
      constructor(options){
          this.vm = new Vue({
              data: {
                  state: options.state
              }
          })
          let getters = options.getters || {}
          this.getters = {}
          Object.keys(getters).forEach((key) => {
              Object.defineProperty(this.getters, key, {
                  get: () => {
                      return getters[key](this.state)
                  }
              })
          })
  
          let mutations = options.mutations || {}
          this.mutations = {}
          Object.keys(mutations).forEach(fnName => {
              this.mutations[fnName] = (arg)=>{
                  return mutations[fnName](this.state, arg)
              }
          })
          //actions
          let actions = options.actions || {}
          this.actions = {}
          Object.keys(actions).forEach((fnName)=>{
              this.actions[fnName] = arg => {
                  return actions[fnName](this, arg)
              }
          })
      }
      get state(){
          return this.vm.state
      }
      commit = (method, arg) => {
          // debugger;
          this.mutations[method](arg)
      }
      dispatch(method, arg){
          this.actions[method](arg)
      }
  
  }
  
  let install = function(v){
      Vue = v
      Vue.mixin({
          beforeCreate () {
              console.log(this)
              if(this.$options && this.$options.store){
                  this.$store = this.$options.store
              }else{
                  this.$store = this.$parent && this.$parent.$store
              }
              console.log(this.$store)
          }
      })
  }
  
  const Vuex = {
      Store,
      install 
  }
  
  export default Vuex
  ````

  

#### vue-router 原理

- **原理**

  [手写原理](https://juejin.cn/post/6854573222231605256)

  ```
//myVueRouter.js
  let Vue = null;
class HistoryRoute {
      constructor(){
        this.current = null
      }
}
  class VueRouter{
    constructor(options) {
          this.mode = options.mode || "hash"
        this.routes = options.routes || [] //你传递的这个路由是一个数组表
          this.routesMap = this.createMap(this.routes)
        this.history = new HistoryRoute();
          this.init()
    }
    init(){
          if (this.mode === "hash"){
            // 先判断用户打开时有没有hash值，没有的话跳转到#/
              location.hash? '':location.hash = "/";
            window.addEventListener("load",()=>{
                  this.history.current = location.hash.slice(1)
            })
              window.addEventListener("hashchange",()=>{
                this.history.current = location.hash.slice(1)
              })
        } else{
              location.pathname? '':location.pathname = "/";
            window.addEventListener('load',()=>{
                  this.history.current = location.pathname
              })
              window.addEventListener("popstate",()=>{
                  this.history.current = location.pathname
              })
          }
      }
  
      createMap(routes){
          return routes.reduce((pre,current)=>{
              pre[current.path] = current.component
              return pre;
          },{})
      }
  
  }
  VueRouter.install = function (v) {
      Vue = v;
      Vue.mixin({
          beforeCreate(){
              if (this.$options && this.$options.router){ // 如果是根组件
                  this._root = this; //把当前实例挂载到_root上
                  this._router = this.$options.router;
                  Vue.util.defineReactive(this,"xxx",this._router.history)
              }else { //如果是子组件
                  this._root= this.$parent && this.$parent._root
              }
              Object.defineProperty(this,'$router',{
                  get(){
                      return this._root._router
                  }
              });
              Object.defineProperty(this,'$route',{
                  get(){
                      return this._root._router.history.current
                  }
              })
          }
      })
      Vue.component('router-link',{
          props:{
              to:String
          },
          render(h){
              let mode = this._self._root._router.mode;
              let to = mode === "hash"?"#"+this.to:this.to
              return h('a',{attrs:{href:to}},this.$slots.default)
          }
      })
      Vue.component('router-view',{
          render(h){
              let current = this._self._root._router.history.current
              let routeMap = this._self._root._router.routesMap;
              return h(routeMap[current])
          }
      })
  };
  
  export default VueRouter
  ```
  
  
  
  - **什么是前端路由**
  
     在web 前端单页应用spa中，路由描述的是url与ui之间的映射关系，这种映射是单向的， 既 url变化引起UI更新（无需刷新页面）
  
  - **如何实现前端路由？**
  
    有两个核心问题，如何改变URL却不引起页面的刷新？ 如何检测URL变化了？
  
    1. **hash实现**
  
       hash是URL中hash(#)及后面的那部分，常用作锚点在页面内导航， 改变url中的hash部分不会引起页面的刷新， 通过hashchange事件监听url的变化，改变url的方式只有这几种： 
  
       通过浏览器前进后退改变URL
  
       通过`<a>`标签改变url
  
       通过window.location改变url
  
    2. history提供了pushState和replaceState两个方法，这两个方法改变URL的path部分不会引起页面的刷新 ，popstate事件监听
  
    ​        通过浏览器前进后退改变URL时会触发popstate事件
  
    ​	   通过pushState/replaceState或`<a>`标签改变URL不会触发popstate事件
  
    ​		我们可以拦截`pushState/replaceState`的调用和`<a>`标签的点击事件来检测URL变化
  
    ​		通过js调用history的back，go，foward方法可以触发该事件
  
    ​		
  
    

`linkActiveClass`  `linkExactActiveClass`  当前router激活时的class,  `linkExactActiveClass`精确激活的class   在 `router-link`标签中使用 `active-class` 当router-link标签被点击时，会应用这个样式

- 怎样定义vue-router的动态路由？怎么获取传过来的值？

  动态路由参数通过动态路径参数，以冒号开头 

  ````
  new VueRouter({routes: [{path: '/user/:id', component: ()=> import('./user')}]})
  ````

  获取参数通过，`this.$route.params.id`

- vue-router有哪几种导航钩子

  全局前置守卫

  ````
  //可以在此做一些权限管理, 进度条
  const router = new VueRouter({})
  router.beforeEach((to, from, next)=>{})
  //next(),进入管道中的下一个钩子， next(false)终止当前的导航， next('/')跳转不同的地址
  
  ````

  全局后置钩子

  ```
  //可以结束进度条, 没有next函数
  router.afterEach((to, from)=>{
  	
  })
  ```

  组件内导航钩子

  ````
  beforeRouterEnter(to, from, next){
  	//在渲染该组件的对应路由被confirm前调用
  	//不能获取组件实例this, 因为当守卫执行前，组件还未被创建
  },
  beforeRouterUpdate(to, from, next){
  	//当前路由改变，该组件被复用时调用
  }，
  beforeRouteLeave(to, from, next){
  	//导航离开前调用
  	//可以访问组件实例this
  }
  ````

- `$router` 和 `$route`的区别是什么

  router为VueRouter的实例， 是一个全局路由对象，包含了路由跳转的方法、钩子函数等。

  route 路由信息对象||跳转到的路由对象，每个路由都会有一个route对象，是一个局部对象，包含了path、params、hash、query、fullPath、matched、name等路由信息参数

- vue-router响应路由参数的变化

  使用watch检测

  ```
  watch:{
  	$route(to, from){
  		console.log(to.path)
  		//监听路由变化
  	}
  }
  ```

  使用组件内导航钩子函数

  ```
  beforeRouteUpdate(to, from, next){
  	//to do something
  }
  ```

- vue-router传参

  **Params**

  只能使用name, 不能使用path 参数不会显示在路由上，浏览器刷新，参数会被清除

  ```
  this.$router.push({
  	name: 'user',
  	params: {
  		name: 'wa'
  	}
  })
  //接收参数
  const p = this.$route.params
  ```

  **query**

  name可以使用path路径

  ```
  //传递参数
  this.$router.push({
  	name: '',
  	query: {
  		number: 1,
  		code: '999'
  	}
  })
  //接收参数
  const q = this.$route.query
  ```

  **路由方式**

  ```
  //注册时
  {
  	path: '/user/:userID/:userName?' //？问号的意思是改参数不是必传项
  }
  //接收参数
  this.$route.params.userID
  ```

- vue-router实现路由懒加载(动态加载路由)

  把不同路由对应的组件分割成不同的代码块，当路由被访问时才加载对应的组件，

  `{component: () => impor('./user')}`

####  v-for `key`的作用

​	[讲述key的还不错](https://juejin.cn/post/6844903826693029895)

- key是为Vue中vnode的唯一标记，通过这个key，我们的`diff`操作可以更准确，更快速。 Vue的diff过程可以概括为： `oldCh`和`newCh`各有两个头尾变量`oldStartIndex` `oldEndIndex` `newStartIndex` `newEndIndex`

  他们的新结点和旧节点会两两进行比较，一共4种比较方式 oldStartIndex和newStartIndex 、oldEndIndex和newEndIndex、 oldStartIndex和newEndIndex、 oldEndIndex和newStartIndex。如果以上4种情况都没匹配，如果设置了key，就会用key进行在比较（在map中）, 如果没有设置key，通过`findIdxInOld`方法遍历oldCh来获取newStartVnode互为sameVnode的oldVnode. 比较过程中遍历会向中间靠拢，一旦 startIdx > EndIdx 表明oldCh和newCh至少有一个已经遍历完了，就会结束比较。

- 更准确： 因为带key就不用就地复用了，在sameNode函数`a.key ===b.key`对比中就可以避免就地复用的情况，所以更准确

- 更快速：利用key的唯一性生成map对象来获取对应的节点，比遍历方式更快，源码：

  ````
  function createKeyToOldIdx (children, beginIdx, endIdx) {
    let i, key
    const map = {}
    for (i = beginIdx; i <= endIdx; ++i) {
      key = children[i].key
      if (isDef(key)) map[key] = i
    }
    return map
  }
  ````

#### vue挂载插件如何没有重复挂载

````javascript
Vue.use = function(plugin){
    const installedPlugins = (this._installedPlugins || (this._installedPlugins = [])); 	if(installedPlugins.indexOf(plugin)>-1){
        return this; 
    } <!-- 其他参数 --> 
    const args = toArray(arguments,1); 
    args.unshift(this); 
    if(typeof plugin.install === 'function'){ 	
        plugin.install.apply(plugin,args);
     }
    else if(typeof plugin === 'function'){ 
        plugin.apply(null,plugin,args); 
    } 
    installedPlugins.push(plugin); return this; 
}
````









 



