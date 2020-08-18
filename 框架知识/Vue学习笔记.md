## 基础语法

### 插值

#### 文本插值

```vue
<div id='app'>{{ msg }}</div>

<script>
	var app = new Vue({
    el: '#app',
    data: {
      msg: 'hello, Vue'
    }
  })
</script>

// 也许你想要插入HTML

<div id="app">
  <p>Using mustaches: {{ rawHtml }}</p>
  <p>Using v-html directive: <span v-html="rawHtml"></span></p>
</div>

<script>
  var app = new Vue({
    el: '#app',
    data: {
      rawHtml: '<span style="color: red">This should be red.</span>',
    }
  })
</script>
```

#### 属性绑定

```vue
<div id='app' v-bind:title='title'>23333</div>
<!-- v-bind可以缩写为如下-->
<div id='app' :title='title'>23333</div>
<!--动态绑定-->
<div id='app' :[attributeName]='attr'>23333</div>

<script>
	var app = new Vue({
    el: '#app',
    data: {
      title: 'you are beautiful'
    }
  })
</script>
```

#### 表单双绑

```vue
<div id='app'><input v-modle='msg' /></div>

<script>
	var app = new Vue({
    el: '#app',
    data: {
      msg: 'Across The Great Wall, we can reach everywhere'
    }
  })
</script>
```

#### 事件绑定

```vue
<div id="app">
  <button v-on:click='answer'>回答</button>
  <!--简写-->
  <button @click='answer'>回答</button>  
  <!--动态绑定-->
  <button @[eventName]='event'>回答</button>
  
  <p v-if="seen">what a beautiful girl!</p>
</div>
  
<script>
  let app = new Vue({
    el: '#app',
    data: {
      seen: false,
    },
    methods: {
      answer: function () {
        this.seen = !this.seen;
      }
    }
  })
</script>
```

#### 类和样式的绑定

https://cn.vuejs.org/v2/guide/class-and-style.html

### 指令

#### 循环

```vue
<div id='app'>
  <ol>
  	<li v-for='(item, key, index) of fruits'>{{ item }}</li>
  </ol>
</div>

<script>
	var app = new Vue({
    el: '#app',
    data: {
      fruits: [
        '🍉',
        '',
        '🍊',
        '🍇'
      ]
    }
  })
</script>
```

#### 条件判断

不同的是带有 `v-show` 的元素始终会被渲染并保留在 DOM 中。`v-show` 只是简单地切换元素的 CSS property `display`。

`v-if` 是“真正”的条件渲染，因为它会确保在切换过程中条件块内的事件监听器和子组件适当地被销毁和重建。

`v-if` 也是**惰性的**：如果在初始渲染时条件为假，则什么也不做——直到条件第一次变为真时，才会开始渲染条件块。

相比之下，`v-show` 就简单得多——不管初始条件是什么，元素总是会被渲染，并且只是简单地基于 CSS 进行切换。

一般来说，`v-if` 有更高的切换开销，而 `v-show` 有更高的初始渲染开销。因此，如果需要非常频繁地切换，则使用 `v-show` 较好；如果在运行时条件很少改变，则使用 `v-if` 较好。

```vue
<div id="app">
  <button v-on:click='answer'>回答</button>
  <p v-if="seen">what a beautiful girl!</p>
</div>
  
<script>
  let app = new Vue({
    el: '#app',
    data: {
      seen: false,
    },
    methods: {
      answer: function () {
        this.seen = !this.seen;
      }
    }
  })
</script>
```

### 计算属性和监听器

#### 计算属性

我们可以将同一函数定义为一个方法而不是一个计算属性。两种方式的最终结果确实是完全相同的。然而，不同的是**计算属性是基于它们的响应式依赖进行缓存的**。只在相关响应式依赖发生改变时它们才会重新求值。这就意味着只要 `message` 还没有发生改变，多次访问 `reversedMessage` 计算属性会立即返回之前的计算结果，而不必再次执行函数。

```vue
<div id="example">
  <p>Original message: "{{ message }}"</p>
  <p>Computed reversed message: "{{ reversedMessage }}"</p>
</div>

<script>
var vm = new Vue({
  el: '#example',
  data: {
    message: 'Hello'
  },
  computed: {
    // 计算属性的 getter
    reversedMessage: function () {
      // `this` 指向 vm 实例
      return this.message.split('').reverse().join('')
    }
  }
})
</script>
```

#### 监听器

```vue
<div id="example">
  <input v-model='msg'/>
</div>

<script>
var vm = new Vue({
  el: '#example',
  data: {
    msg: 'Hello'
  },
  watch: {
    msg: function (newMsg, oldMsg) {
      // do something
    }
  }
})
</script>
```

### 组件

#### 组件注册

##### 全局注册

```Vue
<script>
  // 组件名可采用kebab-case 和 PascalCase
  Vue.component('ComponentName', {})
</script>
```

##### 局部注册

```vue
<script>
  // current.vue
  import ComponentA from './ComponentA.vue'

  export default {
    components: {
      ComponentA
    },
    // ...
  }
</script>
```

##### webpack入口文件导入基础组件

```js
import Vue from 'vue'
import upperFirst from 'lodash/upperFirst'
import camelCase from 'lodash/camelCase'

const requireComponent = require.context(
  // 其组件目录的相对路径
  './components',
  // 是否查询其子目录
  false,
  // 匹配基础组件文件名的正则表达式
  /Base[A-Z]\w+\.(vue|js)$/
)

requireComponent.keys().forEach(fileName => {
  // 获取组件配置
  const componentConfig = requireComponent(fileName)

  // 获取组件的 PascalCase 命名
  const componentName = upperFirst(
    camelCase(
      // 获取和目录深度无关的文件名
      fileName
        .split('/')
        .pop()
        .replace(/\.\w+$/, '')
    )
  )

  // 全局注册组件
  Vue.component(
    componentName,
    // 如果这个组件选项是通过 `export default` 导出的，
    // 那么就会优先使用 `.default`，
    // 否则回退到使用模块的根。
    componentConfig.default || componentConfig
  )
})
```



