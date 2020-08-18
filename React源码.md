## Normal API

### JSX

`JSX`实质上是`React.createElement`的语法糖，在`babel`中`polyfill`如下

**普通`jsx`元素**

1. 标签名为第一个参数
2. 属性为第二个参数
3. `...children`为`3 ~ n`个参数

```jsx
<div class="handsome" id="awesome">
	<span>hello</span>
  <span>world</span>
<div>
  
// 转译后
React.createElement(
  'div',
  {class: "handsome",id: "awesome"},
  React.createElement("span",null,"hello"),
  React.createElement("span",null,"world"),
);  
```

**组件**

组件元素与普通元素相比，**首字母必须大写**，因为转译后会变为一个对象

```jsx
function Compo(){
  return <p>you are beautiful</p>
}
<Compo id="component"><span>hey sweet</span></Compo>
// 转译后
function Compo(){
  return React.createElement("p",null,"you are beautiful");
}
React.createElement(Compo,{id: "component"},React.createElement("span",null,"hey sweet"))
```

### react-element

#### `react-element`的重要属性

- **`$$typeof: REACT_ELEMENT_TYPE`**：标识元素是什么类型的，默认值是`REACT_ELEMENT_TYPE`，也就是说`JSX`创建的节点都是该元素类型。
- **`type`**：元素标签是什么类型的，比如`原生element | class component | hooks `
- **`key`**：用作`virtual dom`标识节点
- **`ref`**：元素的真实`dom`节点
- **`props`**：元素的属性，如`class | id | style`

#### 创建一个`react-element`

```js
function createElement(type,config,children){
  // 参数处理
  // 对于key，ref属于内嵌的属性，不能通过传入的config进行修改的
  // 初始化，单独存储这两个属性
  let key = config ? config.key : null;
  let ref = config ? config.ref : null;
  
  // 过滤内嵌属性key和ref,同时将其他属性存储到props对象上，因此不能通过this.props.ref来访问这些ref
  // RESERVED_PROPS是一个内置对象
  const RESERVED_PROPS = {
    key: true,
    ref: true,
    __self: true,
    __source: true,
  };
  for(propName in config){
    if(hasOwnProperty.call(config,propName) && !RESERVED_PROPS.hasOwnProperty(propName)){		
      props[propName] = config[propName];
    }
  }
  
  // children的处理，简单来说就是将第三个参数及其后的参数加入到props.children
  children.length === 1 ? (props.children = children) : (遍历children添加到props.children中) 
  
  // props默认值的处理，判断是否存在默认属性，有点话就替换掉初始化的属性
   for (propName in defaultProps) {
     if (props[propName] === undefined) {
       props[propName] = defaultProps[propName];
     }
   }  
   return ReactElement(type,key,...,props);
}
```

### Component

**朴实无华的定义部分？？？**

```js
// updater似乎从未用到过，但这个小家伙却是component更新的关键
function Component(props, context, updater) {
  this.props = props;
  this.refs = emptyObject;
  this.updater = updater || ReactNoopUpdateQueue;
}

// 定义耳熟能详的接口，比如setState
Component.prototype.setState = function(partialState, callback) {
  invariant(	// 这里很明显是对第一个参数进行校验，默认为null，可以传入对象和函数
    typeof partialState === 'object' ||
      typeof partialState === 'function' ||
      partialState == null,
  );	
  // 调用更新的接口
  this.updater.enqueueSetState(this, partialState, callback, 'setState');
};

// Component和pureComponent的区别在定义上及其简单
// pureComponent继承自Component
// 区别是pureComponent多了一个属性用于判断是否为纯组件
pureComponentPrototype.isPureReactComponent = true;
```

### Ref

#### 创建`ref`的三种方式

```jsx
// 调用
this.refs.stringRef
this.func
this.objRef.current
// 定义
constructor(){
  this.objRef = React.createRef();
}
<span ref="stringRef">字符串ref</span>
<span ref={(ele) => this.func(ele)}>方法ref</span>
<span ref={this.objRef}>对象ref</span>
```

#### 如何在纯组件中访问`ref`

`pureComponent`通常是用于页面的展示，但偶尔也会有需要操纵`dom`的情况

众所周知`pureComponent`没有`this`，因此不能通过`this.refs`获取实例

通过`forwardRef`可以实现上述需求

```tsx
// 使用函数组件时除props外再传入一个ref参数
const TargetComponent = React.forwardRef((props,ref) => (
	<input type="text" ref={ref}/>
))

// 这里是ts中泛型的写法
function forwardRef<Props, ElementType: React$ElementType>(
  // 可以看出render的功能是接收props和ref两个参数返回一个React节点
  render: (props: Props, ref: React$Ref<ElementType>) => React$Node,
){
    return {
    // 值得注意的是这里也有$$typeof属性，它跟react-element中的$$typeof有所不同
    // 之前提到过jsx语法都是REACT_ELEMENT_TYPEl，这里看上去冲突了，实际并不是，毕竟挂载对象不同
    // 简单来说这里的$$typeof是react-element中type属性的一个属性，而react-element的$$typeof是react元素节点的一个属性  
    $$typeof: REACT_FORWARD_REF_TYPE,
    render,
  };
}
```

### Context

#### 创建`Context`的两种方式

```jsx
// childContextType,即将启用
Parent.childContextType = {
  value: PropTypes.string,
}
// 通过该方法实例化参数
getChildContext(){
  return {value: 'hello,world'}
}
Child.contextType = {
  value: PropType.string,
}
render(){
  console.log(this.context.value);
}

// createContext
import {Provider,Consumer} = React.createContext('default');
function Parent(){
  return <Provider value='i am new provider'>2333</Provider>
}

function Children(){
  return <Consumer>{value => <p>newContext: {value}</p>}</Consumer>
}
```

#### 源码部分

```tsx
const context: ReactContext<T> = {
  $$typeof: REACT_CONTEXT_TYPE,
  _currentValue: defaultValue,	// 始终存储最新的context
  Provider: (null: any),
  Consumer: (null: any),
};

context.Provider = {
  $$typeof: REACT_PROVIDER_TYPE,	// 这里的$$typeof也是react-element的type中的属性
  _context: context,	// Provider的_context指向context对象
};
context.Consumer = context	// context.Consumer指向context本身，也就是说context本身更新，那么context.Consumer也会更新
```

### ConcurrentMode

并发模式是一组新的功能，可以由使用者调度`React`整体渲染的优先级，从而更加优雅的调度资源。

该功能尚处于试验阶段，可能会发生变化

#### 基本使用

```tsx
import {ConcurrentMode} from 'react';
import {flushSync} from 'react-dom';

// 使用flushSync包裹的状态更新优先级更高，其含义是立即调度执行
flushSync(() => this.setState({}))
```

#### 源码部分

```tsx
// 只是简单的定义了一个Symbol类型，OMG
export const REACT_CONCURRENT_MODE_TYPE = hasSymbol
  ? Symbol.for('react.concurrent_mode')
  : 0xeacf;
```

### Suspense

Suspense 不是一个`数据获取库`, 而是一种提供给‘数据获取库’的`机制`，数据获取库通过这种机制告诉 React 数据还没有准备好，然后 React就会等待它完成后，才继续更新 UI。

#### 基本使用

```tsx
// 使用同步写法实现一个Loading页面
function Posts() {
  const posts = useQuery(GET_MY_POSTS)

  return (<div className="posts">
    {posts.map(i => <Post key={i.id} value={i}/>)}
  </div>)
}
// 加载时显示Posts Loading...
// 请求到数据后，进行数据渲染
function App() {
  return (<div className="app">
    <Suspense fallback={<Loader>Posts Loading...</Loader>}>
      <Posts />
    </Suspense>
  </div>)
}

class App extends React.Compoent{
  constructor(porps){
    super(props);
    this.state = {
      data: '',
    }
  }
  
  async componentDidMount(){
    const temp = await axios.get(url);
    this.setState({
      data: temp
    })
  }
  
  return 
  <div>
    {this.state.data.length === 0 ? 'Loading' : this.state.data}
  <div/>
}
```

#### 源码部分

```tsx
// ctor可以看作为一个promise，拥有thenable方法
export function lazy<T, R>(ctor: () => Thenable<T, R>): LazyComponent<T> {
  return {
    $$typeof: REACT_LAZY_TYPE,
    _ctor: ctor,
    // React uses these fields to store the result.
    _status: -1,	// 记录状态，创建时为pending
    _result: null,	// resolve后的返回值
  };
}
```

### Hooks

为函数式组件添加了状态和生命周期

#### 基本使用

```tsx
import React,{useState,useEffect} from 'react';
// useState返回一个状态和一个更新该状态的方法
const [name,setName] = useState('alex');
// 相当于生命周期函数中的componentDidMonut,componentDidUpdate,componentWillUnMount，会在每次更新的时候执行，可以通过传入第二个参数决定是否更新，第二个参数是一个state数组
useEffect(() => {
  // do something
})
```

#### 源码部分

### Children

#### API

```tsx
// React.map不同于原生map，会将返回值的数组进行扁平化处理（一个递归调用的过程）
// 对于第一个参数，会调用一个traverseAllChildrenImpl方法，如果是单节点会直接调用第二参数的callback，否则递归调用traverseAllChildrenImpl
// 与前面类似，回到函数中的参数如果是数组也会进行递归，通过调用mapIntoWithKeyProfixInternal。这样也能解释为什么返回的节点都是一维的
function ChildrenDemo(props){
  React.Children.map(props.children,c => [c,[c,c]])	// 会展开为3个评级react节点
}
```

## React更新机制

### 创建更新的方式

- ReactDOM.render | hydrate
- forceUpdate()
- setState()

#### render和hydrate(调和dom中的节点，类似缓存)

当服务端和客户端渲染不一致时，`render`会做`dom patch`，使得最后的渲染内容和客户端一致。而`hydrate`不会对整个`dom`树做`dom patch`，其只会对`text Content`内容做`patch`，对于属性并不会做`patch`

### fiber

每一个`ReactElement`对应一个`Fiber`对象，可用于记录各节点的状态（使得function component拥有状态），并串联整个应用形成树结构

![react-tree-demo-Fiber树.png](https://i.loli.net/2020/08/03/Nw9qkylJZ3Bni8x.png)

#### `fiberRoot`的数据结构

```typescript
type BaseFiberRootProperties = {
  // root节点，render方法接收的第二个参数
  containerInfo: any,
  // 只有在持久更新中会用到，也就是不支持增量更新的平台，react-dom不会用到
  pendingChildren: any,
  // 当前应用对于的Fiber对象，是Root Fiber
  current: Fiber,
  
  // 以下的优先级是用来区分
  // 1) 没有提交（committed）的任务
  // 2）没有提交的挂起任务
  // 3）没有提交的可能被挂起的任务
  // 我们选择不追踪每个单独的阻塞登记，为了兼顾性能
  // 最老和新的在提交的时候被挂起的任务
  earliestSuspendedTime: ExpirationTime,
  latestSuspendedTime: ExpirationTime,
  
  // 如果有错误被抛出并且没有更多的更新存在，我们尝试在处理错误前同步重新从头渲染
  // 在`renderRoot`出现无法处理的错误是会被设置为`true`
  didError: boolean,
  
  // 正在等待提交的任务的`expirationTime`
  pendingCommitExpirationTime: ExpirationTime,
  // 已经完成的任务的FiberRoot对象，如果你只有一个Root，那他永远只可能是这个Root对应的Fiber，或者是null
  // 在commit阶段只会处理这个值对应的任务,标记渲染完成的fiber
  finishedWork: Fiber | null,
  // 在任务被挂起的时候通过setTimeout设置的返回内容，用来下一次如果有新的任务挂起时清理还没触发的timeout
  timeoutHandle: TimeoutHandle | NoTimeout,
  // 顶层context对象，只有主动调用`renderSubtreeIntoContainer`时才会有用
  context: Object | null,
  pendingContext: Object | null,
  // 用来确定第一次渲染的时候是否需要融合
  +hydrate: boolean,
  // 当前root上剩余的过期时间
  // TODO：提到renderer里面去处理
  nextExpirationTime: ExpirationTime,
  // 当前更新对应的过期时间
  expirationTime: ExpirationTime,
  // 顶层批次（批处理任务）这个变量指明一个commit是否应该被推迟
  // 同时包括完成之后的回调
  // 貌似用在测试的时候
  firstBatch: Batch | null,
  // root之间关联的链表结构、
  nextScheduleRoot: FiberRoot | null,
}
```

#### `fiber`的数据结构

```ts
type Fiber {
	// 标记不同的组件类型
  tag: WorkTag,

  // ReactElement里面的key
  key: null | string,
    
  // ReactElement.type，也就是我们调用`createElement`的第一个参数
  elementType: any,
    
  // 异步组件resolved之后返回的内容，一般是`function`或者`class`
  type: any,
    
  // 跟当前Fiber相关本地状态（比如浏览器环境就是DOM节点）
  stateNode: any,
  
  // 指向他在Fiber节点树中的`parent`，用来在处理完这个节点之后向上返回
    return: Fiber | null,
      
  // 单链表树结构
  // 指向自己的第一个子节点
  child: FIber | null,
  // 指向自己的兄弟结构
  // 兄弟节点的return指向同一个父节点
  sibling: Fiber | null,
  index: number,
  
  // ref属性
  ref: null | (((handle: mixed) => void) & {_stringRef?: string}) | RefObject,
    
  // 新的变动带来的新的props
  pendingProps: any,
  // 上一次渲染完成之后的props
  memoizedProps: any,
    
  // 该Fiber对应的组件产生的Update会存放在这个队列里
  updateQueue: UpdateQueue<any> | null,
    
  // 上一次渲染的时候的state
  memoizedState: any,
    
  // 一个列表，存放这个Fiber依赖的context
  firstContextDependency: ContextDependency<mixed> | null,
    
  // 用来描述当前Fiber和他子树的`Bitfield`
  // 共存的模式表示这个子树是否默认是异步渲染的
  // Fiber被创建的时候他会继承父Fiber
  // 其他的标识也可以在创建的时候被设置
  // 但是在创建之后不应该再被修改，特别是他的子Fiber创建之前
  mode: TypeOfMode,
    
  // Effect
  // 用来记录Side Effect
  effectTag: SideeffectTag,
    
  // 单链表用于快速查找下一个side effect
  nextEffect: Fiber | null,
    
  // 子树中第一个side effect
  firstEffect: Fiber | null,
    
  // 子树中最后一个side effect
  lastEffect: Fiber | null,
    
  // 代表人物在未来的那个时间点应该完成
  // 不包括他的子树产生的任务
  expirationTime: ExpirationTime,
    
  // 快速确定子树中是否有不在等待的变化
  childExpirationTime: ExpirationTime,
    
  // 在Fiber树更新的过程中，每个Fiber都会有一个跟其对应的Fiber
  // 我们称他为`current <==> workInProgress`
  // 在渲染完成之后他们会交换位置
  alternate: Fiber | null,
    
  // 下面是调试相关的，收集每个Fiber和子树渲染时间的
    
  actualDuration?: number,
  actualStartTime?: number,
  treeBaseDuration?: number
}
```

#### `update`数据结构

```ts
type Update<state> = {
  // 更新的时间
  expirationTime: ExpirationTime,
  // export const UpdateState = 0;
  // export const ReplaceState = 1;
  // export const ForceUpdate = 2;
  // export const CaptureUpdate = 3;
  // 指定更新的类型，值为以上几种
  tag: 0 | 1 | 2 | 3,
  // 更新内容，比如`setState`接收的第一个参数
  payload: any,
  // 对应的糊掉，`setState`，`render`都有
  callback: (() => mixed) | null,
  
  // 指向下一个更新
  next: Update<State> | null,
  // 指向下一个`side effect`
  nextEffect: Update<State> | null,
};
```

#### `updateQueue`数据结构

```ts
type UpdateQueue<State> = {
  // 每次操作完更新之后的`state`
  baseState: State,
  
  // 队列中的第一个`Update`
  firstUpdate: Update<State> | null,
  // 队里中最后一个`Update`
  lastUpdate: Update<State> | null,
  
  // 第一个捕获类型的`Update`
  firstCaptureUpdate: Update<State> | null,
  // 最后一个捕获类型的`Update`
  lastCaptureUpdate: Update<State> | null,
  
  // 第一个`side effect`
  firstEffect: Update<State> | null,
  // 最后一个`side effect`
  lastEffect: Update<State> | null,
  
  // 第一个捕获产生的`side effect`
  firstCapturedEffect: Update<State> | null,
  // 最后一个捕获产生的`side effect`
  lastCapturedEffect: Update<State> | null,
}
```

#### enqueueUpdate

```ts
// 进行state数组的更新
export function enqueueUpdate<State>(fiber: Fiber, update: Update<State>) {
  // Update queues are created lazily.
  const alternate = fiber.alternate; // fiber数据结构中的属性，作为workInProgress
  let queue1;
  let queue2;
  if (alternate === null) {
    // There's only one fiber.
    queue1 = fiber.updateQueue;
    queue2 = null; // workInProgress 为空时
    if (queue1 === null) {
      queue1 = fiber.updateQueue = createUpdateQueue(fiber.memoizedState); // 初始化更新队列
    }
  } else {
    // There are two owners.
    queue1 = fiber.updateQueue;
    queue2 = alternate.updateQueue;
    if (queue1 === null) {
      if (queue2 === null) {
        // Neither fiber has an update queue. Create new ones.
        // 跟alternate === null时一致，fiber.updateQueue为空时将进行初始化
        queue1 = fiber.updateQueue = createUpdateQueue(fiber.memoizedState);
        queue2 = alternate.updateQueue = createUpdateQueue(
          alternate.memoizedState,
        );
      } else {
        // Only one fiber has an update queue. Clone to create a new one.
        queue1 = fiber.updateQueue = cloneUpdateQueue(queue2);
      }
    } else {
      if (queue2 === null) {
        // Only one fiber has an update queue. Clone to create a new one.
        queue2 = alternate.updateQueue = cloneUpdateQueue(queue1);
      } else {
        // Both owners have an update queue.
      }
    }
  }
  if (queue2 === null || queue1 === queue2) {
    // There's only a single queue.
    appendUpdateToQueue(queue1, update); // 放置于更新队列的末尾
  } else {
    // There are two queues. We need to append the update to both queues,
    // while accounting for the persistent structure of the list — we don't
    // want the same update to be added multiple times.
    if (queue1.lastUpdate === null || queue2.lastUpdate === null) {
      // One of the queues is not empty. We must add the update to both queues.
      appendUpdateToQueue(queue1, update);
      appendUpdateToQueue(queue2, update);
    } else {
      // Both queues are non-empty. The last update is the same in both lists,
      // because of structural sharing. So, only append to one of the lists.
      appendUpdateToQueue(queue1, update);
      // But we still need to update the `lastUpdate` pointer of queue2.
      queue2.lastUpdate = update;
    }
  }
}
```





