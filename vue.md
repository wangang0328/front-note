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

  

### $nextTick

### keep-alive

### mixin



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







