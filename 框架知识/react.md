### react的特点
1. 声明式设计，可以轻松的描述应用
    - 关注的是你要做什么，而不是如何做
    - 命令式编程是面向过程编程
    - 函数式编程是指通过复合纯函数来构建软件的过程
2. 灵活性：能很好地与现有框架和库兼容
3. 高效性：采用虚拟dom技术减少了对dom的操作
4. 使用jsx语法
    - 生成的react元素并不是真实的dom元素，而是一个js对象，在render时将当前加载的所有js对象转换为真实的dom节点插入到真实的dom树中
5. 组件化开发思想
6. 单向响应数据流，对数据是单向绑定

### pure component
1. 不需要手动在shouldComponentUpdate里面去判断，当prop和state变化时自动会触发render。通过shallowEqual，用Object.key比较组件的key值和state值。
2. 数据为对象时必须地址不同才能重新render

### 虚拟dom（diff）
#### 三个策略
1. dom操作中跨层级的操作很少，忽略不计
2. 同一类的两个组件生成两个相似的dom结构，不同类的两个组件生成不同的dom结构
3. 同一层级的一组子节点通过id区分

#### tree diff
- 针对策略1，react只会对相同层级的节点进行比较，即同一父节点下的所有子节点。当比较时发现该节点不见时，会删除该节点极其下所有子节点而不继续向下比较，这样通过一次遍历就能完成dom树的比较。
- 对于跨层级操作，会先删除缺失的节点极其所有子节点，然后在添加新增的节点极其所有子节点，然后重新渲染dom。官方不推荐这样做

#### component diff
- 对于同一类型的组件，按原策略进行虚拟dom的比较
- 对于不同类型的组件，将其标记为dirty component并删除该组件极其下所有子组件。
- 对于同一类型的组件，如果提前知道其虚拟dom结构并没有发生变化，可以shouldComponentUpdate()里面判断是否需要使用diff算法进行判断，但是如果使用了forceUpdate()，前方法会失效。

#### element diff
- 新的元素不在旧的集合中，进行插入操作
- 旧的元素在新的结合中但元素已发送改变则对该元素进行删除操作，对于旧集合存在，新集合不存在的元素执行删除操作
- 元素在新旧集合中都存在但位置发生了改变  
  ![image](http://jiangliuer.vip/download/image/diff1.png)

| index | 节点 | oldIndex | maxIndex | 操作                                              |
| ----- | ---- | -------- | -------- | ------------------------------------------------- |
| 0     | B    | 1        | 0        | oldIndex(1)>maxIndex(0),maxIndex=oldIndex         |
| 1     | A    | 0        | 1        | oldIndex(0)<maxIndex(1),节点A移动至index(1)的位置 |
| 2     | D    | 3        | 1        | oldIndex(3)>maxIndex(1),maxIndex=oldIndex         |
| 3     | C    | 2        | 3        | oldIndex(2)<maxIndex(3),节点C移动至index(2)的位置 |

### 单向数据流，双向数据流
- ui行为→触发action→改变数据state→mumtation再次渲染ui界面，通常就是基于view层
- v层能让m层变了，m层也能让v层变了
### react生命周期
![image](http://jiangliuer.vip/download/image/reactLifecircle.jpg)
### react新生命周期
![image](https://user-gold-cdn.xitu.io/2019/12/15/16f0a0b3e20fa9aa?imageslim)
1. 设立componentReceiveProps,componentWillUpdate,componentWillMount为unsafe的方法
2. 使用getDerivedStateFromProps替换componentReceiveProps,使用getSnapshotBeforeUpdate替换componentWillUpdate
3. getDerivedStateFromProps返回一个对象，默认返回null.因为没有this需要配合componentDidUpdate使用
### react组件传参
1. 父组件向子组件传值:主要是利用props来进行交流
2. 子组件向父组件传值：子组件通过控制自己的state然后告诉父组件的点击状态。然后在父组件中展示出来
3. 没有任何嵌套关系的组件之间传值：如果组件之间没有任何关系，组件嵌套层次比较深（个人认为 2 层以上已经算深了），或者你为了一些组件能够订阅、写入一些信号，不想让组件之间插入一个组件，让两个组件处于独立的关系。对于事件系统，这里有 2 个基本操作步骤：订阅（subscribe）/监听（listen）一个事件通知，并发送（send）/触发（trigger）/发布（publish）/发送（dispatch）一个事件通知那些想要的组件。
### 受控组件和非受控组件
1. 受状态控制的组件，必须具有onChange方法，通过修改state实现双向数据绑定
2. 非受控意味着我们不需要设置它的state属性，而是通过ref来操作真实dom

### react事件机制
![image](http://jiangliuer.vip/download/image/reactEvent.png)


### react高阶组件
[more](https://juejin.im/post/5e169204e51d454112714580)
1. 高阶组件是一个函数接受一个组件返回一个组件
2. 使用高阶组件可以让代码变得更加优雅，同时增强代码的复用性和灵活性。页面复用，权限控制，组件渲染性能追踪。
3. 实现高阶组件的两种方法
    - 属性代理是从“组合”的角度出发，这样有利于从外部去操作 WrappedComponent，可以操作的对象是 props，或者在 WrappedComponent 外面加一些拦截器，控制器等。
    - 反向继承则是从“继承”的角度出发，是从内部去操作 WrappedComponent，也就是可以操作组件内部的 state ，生命周期，render函数等等。

| 功能列表                             | 属性代理 | 反向继承 |
| ------------------------------------ | -------- | -------- |
| 原组件能否被包裹                     | √        | √        |
| 原组件是否被继承                     | ×        | √        |
| 能否读取/操作原组件的 props          | √        | √        |
| 能否读取/操作原组件的 state          | 乄       | √        |
| 能否通过 ref 访问到原组件的 dom 元素 | 乄       | √        |
| 是否影响原组件某些生命周期等方法     | √        | √        |
| 是否取到原组件 static 方法           | √        | √        |
| 能否劫持原组件生命周期方法           | ×        | √        |
| 能否渲染劫持                         | 乄       | √        |

### redux原理
1. 使用一个store管理所有公共状态
2. 使用getState获取当前状态
3. 使用dispatch更新状态
4. 使用subscribe监听store的状态
```js
import reducer from './reducer'

export const createStore = () => {
    let currentState = [];
    let observers = [];
    function getState(){
        return currentState;
    }
    function dispatch(action){
        currentState = reducer(currentState,action);
        observers.forEach(foo => foo());
    }
    function subscribe(foo){
        observers.push(foo);
    }
    dispatch({type:'INITTYPE'});
    return {getState,dispatch,subscribe};
}
```
- react-redux
1. 使用Provider组件，接收一个store并放进全局的context对象里
2. 使用connect将getState,dispatch方法合并到了this.props中，并自动订阅更新

- 中间件中的洋葱模型
  类似于node.js模块化
  ![image](http://jiangliuer.vip/download/image/onion.jpg)
  [more](https://juejin.im/post/5def4831e51d45584b585000#heading-7)

### React中有几种创建组件的方式
1. 函数式
```js
function HelloComponent(props) {
  return <div>Hello {props.name}</div>
}
ReactDOM.render(<HelloComponent name="yourName" />, mountNode)
```
2. es5
```js
var React = require("react");
var ReactDOM = require("react-dom");

var Hello = React.createClass({
    render(){
        return(
                <div>
                    33333333333
                </div>
        )
    }
})
```
3. es6
```js
import React from "react";
import ReactDOM  from "react-dom";

class Hello extends React.Component{
    render(){
        return(
            <div>
                22222
                <input text="text" ref="username"/>
                <button onClick={this.handleClick.bind(this)}>click</button>
                <button onClick={()=>{
                    console.log(this.refs.username)
                }}>clicktwo</button>
            </div>
        )
    }
    handleClick(){
        console.log(444444444);
        console.log(this.refs.username)
    }
}

```

### react hook
[more](https://juejin.im/post/5d70ac07e51d4561fa2ec0df#heading-6)
1. 对于函数式组件使其增加了状态
2. 使用useState(参数)声明一个state，返回一个state变量和该变量的更新函数（state hook）
3. effect hook相当于生命周期函数中的componentDidMonut,componentDidUpdate,componentWillUnMount，会在每次更新的时候执行，可以通过传入第二个参数决定是否更新，第二个参数是一个state数组
4. hook本质是js函数，react为其制定了两条规则。一是hook只能在最顶层使用，不能在条件，循环，嵌套中使用；二是只能在react函数中使用hook

### 你真的理解setstate()？
#### 参数
1. 两个参数，第一个是一个对象或函数，第二个是回调函数
2. 当前状态更改不依赖上一个状态使用对象，反之使用函数。render后调用回调函数。
#### 如果想要基于最新的state做业务怎么实现
1. componentDiaUpdate里面获取
2. setState的回调函数
#### setState更新组件的流程图
- 通过isBatchingUodates变量判断是直接更新还是异步，在react调用自身的事件处理函数之前会调用batchedUpdate，该方法会将isBatcingUpdate设置为true。  
  ![image](https://user-gold-cdn.xitu.io/2018/8/30/1658a8a62d6bb975?imageslim)
### react context 
[more](https://blog.csdn.net/dianyingtan8093/article/details/102032341?utm_source=distribute.pc_relevant.none-task)
使得组件具有事件捕获的特性。
```react
//父组件
  static childContextTypes = {
    themeColor: PropTypes.string
  }
//子组件  
    static contextTypes = {
    themeColor: PropTypes.string
  }
  this.context.themeColor
```

### react-router实现原理
1. hash路由基于锚点，实现简单，兼容性好，不需要服务端进行特殊配置
2. 基于history的browser路由，使用replaceState，pushState控制url前进；使用popState控制url后退。用sessionState存储状态。url优雅美观但如果想在一个服务器中配置两个不相干项目，并且一个项目占用了根目录，会出现bug。


### redux中间件怎么写，怎么用

```react
// 中间件接受一个对象，里面有原始的 dispatch，和 getState 方法用于获取 state
// 中间件函数返回一个函数，这个函数接受一个 next 参数，这个 next 是下一个中间件要做的事情 action => { ... }
const middleWare = function(){
    return ({dispatch, getState}) => next => action => {
        console.log('中间件2')
        return next(action)
    }
}

const store = createStore(reducer, applyMiddleware(thunkMiddleware));
```

### 为什么会有单向数据流，解决了什么
1. 使用一个上传数据流和一个下传数据流进行双向数据通信，两个数据流之间相互独立。单向数据流指只能从一个方向来修改状态.
2. 流动方向便于数据追踪，便于测试

### react的一些坑点
1. jsx中return的对象中条件判断需要强制类型转换
2. 遍历子节点的时候，不要用index作为组件的key进行传入
3. setState对于react事件并不会立即执行，对于原生事件会立即执行

### react的key
渲染列表时常常会使用map进行子节点遍历渲染，这是控制台会建用加上key值，key值用于diff算法中相同key的组件的属性有所变化只改属性，key不同则先销毁该组件然后再重新创立新的组件

### react fiber架构
**链表树遍历算法**: 通过 **节点保存与映射**，便能够随时地进行 停止和重启，这样便能达到实现任务分割的基本前提；

- 1、首先通过不断遍历子节点，到树末尾；
- 2、开始通过 sibling 遍历兄弟节点；
- 3、return 返回父节点，继续执行2；
- 4、直到 root 节点后，跳出遍历；

