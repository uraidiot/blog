---
title: Vue-Doc
date: 2020-10-20 21:49:00
tags:
---
#### 计算属性`computed`

1. 与 `methods` 比较,  `computed` 具有缓存, 在 `data` 未发生改变时, 多次执行 `computed` 不执行函数, 直接返回结果

2. `computed` 同样具有 `setter`

   ```javascript
   eg.
   computed: {
   	fullName: {
   		get: function () {
   			return `${this.firstName} ${this.lastName}`
   		}
   		set: function (newVal) {
   			let names = newVal.split(' ')
   			this.firstName = names[0]
   			this.lastName = names[1]
   		}
   	}
   }
   ```

#### 侦听器`watch`

> 当需要在数据变化时执行异步或开销较大的操作时, 可使用`watch` , 而计算属性无法做到的

```javascript
eg.
watch: {
  question: function(newQuestion, oldQuestion) {
    this.doSomething() // something like AJAX
  }
}

methods: {
  doSomething: function() {
    /*
    	巴拉巴拉~
    */
  }
}
```

#### 组件

1. 父组件向子组件传递数据`props`, `Array`和`Object`类型的`prop`是以*引用*方式传入,如果子组件修改了此种类型的`prop`可能会影响父组件的展示

2. 子组件向父组件`emit`事件

   ```vue
   eg.
   Vue.component('parent-component', {
   	props: [],
   	template: `
   		<div>
       	<button @click="$emit('some-event', params)">
           Click Me
         </button>   
   		</div>
   	`
   })
   
   <parent-component @some-event="handleEvent"/>
   
   methods: {
   	handleEvent: function(params) {
   		// do something
   	}
   }
   ```

3. 组件支持`slot`, 后续开发中, 可总结什么情况下使用插槽功能
4. 组件支持全局注册or局部注册

#### 处理边界情况

1. `ref` 与 `v-for` 一起使用时, 会得到一个包含了对应数据源的这些子组件的数组

2. 依赖注入

   ```vue
   eg.
   <grand-father>
   	<father>
     	<child />
     </father>
   </grand-father>
   
   // `child`要使用`grand-father`中的方法, 可通过`this.$parent.$parent` 访问. 若突然改变层级, 则会导致出错. 此时可以使用依赖注入
   
   // grand-father 中
   provide: function() {
   	return {
   		someMethod: this.someMethod
   	}
   }
   
   // child 中
   inject: ['someMethod']
   ```

   

#### 内置动画

可参考  [进入/离开&列表过渡](https://cn.vuejs.org/v2/guide/transitions.html)

#### 可复用性

##### 混入

> 混入 (mixin) 提供了一种非常灵活的方式，来分发 Vue 组件中的可复用功能。一个混入对象可以包含任意组件选项。当组件使用混入对象时，所有混入对象的选项将被“混合”进入该组件本身的选项。

- 选项合并

> 当组件和混入对象含有同名选项时，这些选项将以恰当的方式进行“合并”。

1. 数据对象在内部会进行递归合并,并在发生冲突时,**以组件数据优先**

```vue
var mixin = {
  data: function () {
    return {
      message: 'hello',
      foo: 'abc'
    }
  }
}

new Vue({
  mixins: [mixin],
  data: function () {
    return {
      message: 'goodbye',
      bar: 'def'
    }
  },
  created: function () {
    console.log(this.$data)
    // => { message: "goodbye", foo: "abc", bar: "def" }
  }
})
```

2. 同名钩子函数将合并为一个数组，因此都将被调用。混入对象的钩子将在组件自身钩子**之前**调用

```
var mixin = {
  created: function () {
    console.log('混入对象的钩子被调用')
  }
}

new Vue({
  mixins: [mixin],
  created: function () {
    console.log('组件钩子被调用')
  }
})

// => "混入对象的钩子被调用"
// => "组件钩子被调用"
```

3. 值为对象的选项，例如 `methods`、`components` 和 `directives`，将被**合并为同一个对象**。两个对象键名冲突时，取**组件对象**的键值对。

```
var mixin = {
  methods: {
    foo: function () {
      console.log('foo')
    },
    conflicting: function () {
      console.log('from mixin')
    }
  }
}

var vm = new Vue({
  mixins: [mixin],
  methods: {
    bar: function () {
      console.log('bar')
    },
    conflicting: function () {
      console.log('from self')
    }
  }
})

vm.foo() // => "foo"
vm.bar() // => "bar"
vm.conflicting() // => "from self"

```

**注意: ** `Vue.extend()` 也使用同样的策略进行合并。

- 全局混入

> 混入也可以进行全局注册。使用时格外小心！一旦使用全局混入，它将影响**每一个**之后创建的 Vue 实例。

推荐将其作为[插件](https://cn.vuejs.org/v2/guide/plugins.html)发布，以避免重复应用混入。

**注意:**  *请谨慎使用全局混入，因为它会影响每个单独创建的 Vue 实例 (包括第三方组件)*

- 自定义选项合并策略

> 自定义选项将使用默认策略，即简单地覆盖已有值。

如果想让自定义选项以自定义逻辑合并，可以向`Vue.config.optionMergeStrategies` 添加一个函数

```
Vue.config.optionMergeStrategies.myOption = function (toVal, fromVal) {
  // 返回合并后的值
}
```

#### 自定义指令

[参考地址](https://cn.vuejs.org/v2/guide/custom-directive.html)

#### 渲染函数 & JSX

[参考地址](https://cn.vuejs.org/v2/guide/render-function.html)

#### 插件

> 插件通常用来为 Vue 添加全局功能。

[参考地址](https://cn.vuejs.org/v2/guide/plugins.html)

#### 过滤器

> Vue.js 允许你自定义过滤器，可被用于一些常见的文本格式化。

1. 使用地方

过滤器可以用在两个地方：**双花括号插值和 `v-bind` 表达式** (后者从 2.1.0+ 开始支持)。

2. 使用方式

过滤器应该被添加在 JavaScript 表达式的尾部，由“管道”符号指示

```
<!-- 在双花括号中 -->
{{ message | capitalize }}

<!-- 在 `v-bind` 中 -->
<div v-bind:id="rawId | formatId"></div>
```

3. 定义方式

   - 组件选项中定义本地过滤器

   ```
   
   filters: {
     capitalize: function (value) {
       if (!value) return ''
       value = value.toString()
       return value.charAt(0).toUpperCase() + value.slice(1)
     }
   }
   
   ```

   

   - 创建`Vue`实例之前全局定义过滤器

   ```
   Vue.filter('capitalize', function (value) {
     if (!value) return ''
     value = value.toString()
     return value.charAt(0).toUpperCase() + value.slice(1)
   })
   
   new Vue({
     // ...
   })
   ```

   

   **注意:**  *当全局过滤器和局部过滤器重名时，会采用局部过滤器。*

   

   4. 其他

   **注意:** *过滤器函数总接收表达式的值 (之前的操作链的结果) 作为**第一个参数***

   过滤器可以串联:

   ```
   {{ message | filterA | filterB }}	
   
   filterA 被定义为接收单个参数的过滤器函数，表达式 message 的值将作为参数传入到函数中。然后继续调用同样被定义为接收单个参数的过滤器函数 filterB，将 filterA 的结果传递到 filterB 中
   ```

   过滤器是 JavaScript 函数，因此可以接收参数

   ```
   {{ message | filterA('arg1', arg2) }}
   
   filterA 被定义为接收三个参数的过滤器函数。其中 message 的值作为第一个参数，普通字符串 'arg1' 作为第二个参数，表达式 arg2 的值作为第三个参数。
   ```

   
