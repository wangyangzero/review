### 本质

tree-sharking的本质是通过静态检查，判断出冗杂代码（不影响输出），然后消除这些代码。检查的依据是通过分析程序流，判断哪些变量未被使用、引用进而删除代码。

### 为什么与ES6密切绑定

**ES6 module的特点**

- 只能作为模块顶层的语句出现
- 模块之间的依赖关系是可以确定的，与运行时的状态无关
- import的模块名只能是字符串常量

### webpack旧版本为什么会出现tree-sharking看似失效的情况

原因是函数式编程带来的副作用，一种简单的情况是未被使用的函数引用了外部的变量。

而且使用webpack打包时通常会使用babel进行重新编码以适应各种版本，在babel转码时也会带来一些副作用

```js
export class Person {
  constructor ({ name, age, sex }) {
    this.className = 'Person'
    this.name = name
    this.age = age
    this.sex = sex
  }
  getName () {
    return this.name
  }
}
export class Apple {
  constructor ({ model }) {
    this.className = 'Apple'
    this.model = model
  }
  getModel () {
    return this.model
  }
}
// main.js
import { Apple } from './components'

const appleModel = new Apple({
  model: 'IphoneX'
}).getModel()

console.log(appleModel)

```

### 在webpack中配置tree-sharking

[tree-sharking配置](https://webpack.js.org/guides/tree-shaking/#root)

