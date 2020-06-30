## 蛋疼的对象

```js
let a = {name: 'xxx'};
let b = a;
b.name = 'yyy';
console.log(a)	// yyy
```

因为引用对象赋值时为浅拷贝，我们想要实现基于引用类型`a`通常只能自写一个`deepClone`，但这样对内存开销又不太友好。。。

## 特点

### 持久化数据结构

一个数据，在被修改时，仍然能够保持修改前的状态，从本质来说，这种数据类型就是不可变类型，即`immutable`

`immutable`提供了十余种不可变的类型（List，Map，Set，Seq，Collection，Range...）

### 结构共享

immutable使用先进的tries(字典树)技术实现结构共享来解决性能问题，当我们对一个Immutable对象进行操作的时候，ImmutableJS会只clone该节点以及它的祖先节点，其他保持不变，这样可以共享相同的部分，大大提高性能。

### 什么是tries

![tries.png](https://i.loli.net/2020/06/30/H43sgcybfLQxqSr.png)

1. 如图所示为一个字典树结构对象，定点为`root`节点，每个节点都有一个唯一标识（在`immutable`中为`hashcode`）
2. 当我们想要查找某个值如`data.in`时，只需要根据标记`i , n`的路径，就可以找到包含5的节点，而不需要遍历所有节点（类似一个状态机）
3. 当我们想要基于旧的对象生成新的对象（部分属性值有更改）时，如图`newData`会创建一个新的`tea`节点，然后其他的节点还是与旧的对象共享，而旧的对象保持不变。基于这一点开篇那个蛋疼的对象就可以解决了

### 惰性操作

```js
let seqs = Immutable.Seq.of(1, 2, 3, 4, 5, 6, 7, 8)
		.filter(function(x) {
      console.log('immutable对象被执行！')
      return x % 2;
    }).map(x => x * x);
console.log(seqs.get(1));	// 9

// immutable对象被执行！只会执行三次
```

原理是，用`seq`创建的对象，其代码块并没有执行，仅仅是声明了，当调用到`get(1)`时才会执行。

当取到`index = 1`的数之后，后面的就不会再执行了，所以在`filter`时，第三次就取到了要的数，从`4 -> 8`都不会再执行

### API

```js
//Map()  原生object转Map对象 (只会转换第一层，注意和fromJS区别)
immutable.Map({name:'danny', age:18})

//List()  原生array转List对象 (只会转换第一层，注意和fromJS区别)
immutable.List([1,2,3,4,5])

//fromJS()   原生js转immutable对象  (深度转换，会将内部嵌套的对象和数组全部转成immutable)
immutable.fromJS([1,2,3,4,5])    //将原生array  --> List
immutable.fromJS({name:'danny', age:18})   //将原生object  --> Map

//toJS()  immutable对象转原生js  (深度转换，会将内部嵌套的Map和List全部转换成原生js)
immutableData.toJS();

//查看List或者map大小  
immutableData.size  或者 immutableData.count()

// is()   判断两个immutable对象是否相等
immutable.is(imA, imB);

//merge()  对象合并
var imA = immutable.fromJS({a:1,b:2});
var imA = immutable.fromJS({c:3});
var imC = imA.merge(imB);
console.log(imC.toJS())  //{a:1,b:2,c:3}

//增删改查（所有操作都会返回新的值，不会修改原来值）
var immutableData = immutable.fromJS({
    a:1,
    b:2，
    c:{
        d:3
    }
});
var data1 = immutableData.get('a') //  data1 = 1  
var data2 = immutableData.getIn(['c', 'd']) // data2 = 3   getIn用于深层结构访问
var data3 = immutableData.set('a' , 2);   // data3中的 a = 2
var data4 = immutableData.setIn(['c', 'd'], 4);   //data4中的 d = 4
var data5 = immutableData.update('a',function(x){return x+4})   //data5中的 a = 5
var data6 = immutableData.updateIn(['c', 'd'],function(x){return x+4})   //data6中的 d = 7
var data7 = immutableData.delete('a')   //data7中的 a 不存在
var data8 = immutableData.deleteIn(['c', 'd'])   //data8中的 d 不存在
```

### 优缺点

#### 优点：

- 降低mutable带来的复杂度
- 节省内存
- 历史追溯性（时间旅行）：时间旅行指的是，每时每刻的值都被保留了，想回退到哪一步只要简单的将数据取出就行，想一下如果现在页面有个撤销的操作，撤销前的数据被保留了，只需要取出就行，这个特性在redux或者flux中特别有用
- 拥抱函数式编程：immutable本来就是函数式编程的概念，纯函数式编程的特点就是，只要输入一致，输出必然一致，相比于面向对象，这样开发组件和调试更方便

#### 缺点

- 需要重新学习api
- 资源包大小增加（源码5000行左右）
- 容易与原生对象混淆：由于api与原生不同，混用的话容易出错。

## 实战

[Immutable.js 以及在 react+redux 项目中的实践](https://juejin.im/post/5948985ea0bb9f006bed7472#comment)

