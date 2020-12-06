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







