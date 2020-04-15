###  数组

#### 稀疏数组

稀疏数组时指包含从0开始的不连续索引的数组。如果数组是稀疏的，则length属性值大于元素的个数
```
a = new Array(5);   //数组没有元素但length为5
a = [];     //创建一个空数组，length=0
a[1000] = 0;    //赋值添加一个元素，length为1001
```
数组长度有两个特性，一是它总是大于每个元素的索引值，二是当数组长度小于等于某些元素索引值时会删除这些元素。
```
let arr = [1,2,3,4,5];  // 从5个元素的数组开始
arr.length = 3; //现在的arr为[1,2,3]
arr.length = 0;   //删除所有的元素，arr为[]
arr.length = 5;   //长度为5，但是没有元素，就像new Array(5)
```

#### for和forEach
1. for 循环没有任何额外的函数调用栈和上下文；
2. forEach函数签名实际上是array.forEach(function(currentValue, index, arr), thisValue)
    它不是普通的 for 循环的语法糖，还有诸多参数和上下文需要在执行的时候考虑进来，这里可能拖慢性能；

#### filter,every,some,reduce,reduceRight
filter类似于forEach和map，都是对数组进行循环，但filter()会跳过稀疏数组中缺少的元素，所以它的返回数组总是稠密的。
```
let arr = [1,,2,3];
some = arr.filter(function(x){return x < 2});   //[1]
```
every()方法就像数学中的∀
```
let arr = [1,2,3,4]
arr.every(function(x){return x > 0;})  //true
arr.every(function(x){return x % 2 === 0;})   //false
```
some()方法就像数学中的∃
```
let arr = [1,2,3,4]
a.some(function(x){return x > 10;})  //false
a.some(function(x){return x % 2 === 0;})   //true
```
reduce和reduceRight十分类似都是使用指定的函数将数组元素进行重新组合，不过reduceRight是按索引从高到底进行。
```
//数组求和
let sum = arr.reduce(function (x, y) {
    return x + y;
},0);
//数组求最大值
let max = arr.reduce(function (x, y) {
    return Math.max(x,y);
});
//数组去重
var newArr = arr.reduce(function (x, y) {
    x.indexOf(y) === -1 && x.push(y);
    return x;
},[]);
```

### 暂时性死区
在代码块内，使用let和const声明变量之前，该变量都是不可用的，称为暂时性死区。

### let,const,var
let,const具有块级作用域，同时拥有暂时性死区的特性。  
const声明的变量的值原理上是变量所指向的内存地址所存储的数据不变，所以当const声明一个基本类型值是它就不能修改，而声明一个对象时，地址上保存的是该对象的地址，它所指向的对象的数据结构是可变的。

### promise
- promise是js异步的一种方法，出现初衷是为了解决回调地狱。  
- 基本使用
```
let promise = new Promise(function(){
    resolve();
});
```
- 常用的方法有resolve,reject,then.catch
1. resolve接收参数为Promise对象的话会返回这个Promise，接收一个thenable的对象则返回它的最终状态，接收其他值则以该值为成功状态返回一个promise
2. rejected类似于resolve，不过其状态时rejected
3. then是promise的链式调用，能够获取上一个promise resolve的参数
4. catch获取rejected里的参数，通常是异常
- promise事件循环见宏任务，微任务
- 类似方法async和generator

#### promise灵魂拷问
1. promise怎么解决回调地狱的
    1. 回调函数不是直接声明的，而是在通过后面的 then 方法传入的，即延迟传入。这就是回调函数延迟绑定
    2. 我们会根据 then 中回调函数的传入值创建不同类型的Promise, 然后把返回的 Promise 穿透到外层, 以供后续的调用。这里的 x 指的就是内部返回的 Promise，然后在 x 后面可以依次完成链式调用。
    3. 错误冒泡。
2. 为什么promise引入微任务
    1. 采用异步回调替代同步回调解决了浪费 CPU 性能的问题。
    2. 放到当前宏任务最后执行，解决了回调执行的实时性问题。

#### 如何让Promise.all在抛出异常后依然有效

```javascript
Promise.all([a,b,c].map(p => p.catch(e => {...})))
  .then(res => {...})
  .catch(err => {...});
```

### symbol

- basic
```
let name = Symbol('xiaohesong')
typeof name // 'symbol'
let obj = {}
obj[name] = 'xhs'
console.log(obj[name]) //xhs
```
- symbol.for
```
let uid = Symbol.for("uid");
let object = {
    [uid]: "12345"
};

console.log(object[uid]);       // "12345"
console.log(uid);               // "Symbol(uid)"

let uid2 = Symbol.for("uid");

console.log(uid === uid2);      // true
console.log(object[uid2]);      // "12345"
console.log(uid2);              // "Symbol(uid)"

```
- symbol.keyfor
```
let uid = Symbol.for("uid");
console.log(Symbol.keyFor(uid));    // "uid"

let uid2 = Symbol.for("uid");
console.log(Symbol.keyFor(uid2));   // "uid"

let uid3 = Symbol("uid");
console.log(Symbol.keyFor(uid3));   // undefined

```
### 箭头函数
#### 箭头函数与普通函数的区别
1. 箭头函数没有 this，所以需要通过查找作用域链来确定 this 的值。以最近的this作为自己的this
2. 箭头函数没有自己的 arguments 对象，这不一定是件坏事，因为箭头函数可以访问外围函数的 arguments 对象：
3. 不能通过 new 关键字调用，没有没有 new.target（new命令作用于的那个构造函数）
4. 没有原型，没有super
#### 应用场景
1. 上下文相关回调函数不能使用（this指向window），
2. 对象方法不行（this绑定对象）
3. 不能用作构造函数
4. 可以用作类方法供自身使用
### 为什么style内联在header而script在body后
#### css
  首先整个页面展示给用户会经过html 的解析与渲染过程。
  而外链css无论放在html的任何位置都不影响html的解析，但是影响html的渲染。
  如果将css放在尾部，html的内容可以第一时间显示出来，但是会阻塞html行内css的渲染

#### js
  对于那些需要很多JavaScript代码的页面来说，这无疑会导致浏览器在呈现页面时出现明显的延迟，而延迟期间的浏览器窗口中将是一片空白。为避免这个问题，现代web应用程序一般都把全部JavaScript引用放在`<body>`元素中页面内容的后面。
### 浏览器同源策略
  如果两个页面的协议，端口（如果有指定）和主机都相同，则两个页面具有相同的源。
  IE中授信范围（即高度信任）内的两个域名不受同源规则影响
  IE中端口也不受同源的影响

### 事件流
  事件捕获 => 处于目标阶段 => 事件冒泡
### defer和async
#### defer
  会随着文档的解析和渲染异步加载js文件，但会等到页面解析渲染完毕之后执行js，js执行顺序与代码的书写顺序一致
#### async
  会随着文档的解析和渲染异步加载和执行js脚本，js执行顺序和代码的书写顺序可能不一致
### 事件冒泡和事件捕获
#### 事件冒泡（false）
  - 事件冒泡是目标元素接受事件，然后逐级向上传播到顶级元素为止
  - 阻止冒泡：在W3c中，使用stopPropagation()方法；在IE下设置cancelBubble = true

#### 事件捕获（true）
  - 事件捕获与事件冒泡是相反的，是顶级元素先接收到事件，再逐级向下传播到目标元素为止
  - 阻止捕获：阻止事件的默认行为，例如click - <a>后的跳转。在W3c中，使用preventDefault()方法，在IE下设置window.event.returnValue = false

### clientWidth,offsetWidth,scrollWidth
  clientWidth = width + 左右padding 
  offsetWidth = width + 左右padding + 左右border 
  scrollWidth = 可视宽度 + 隐藏宽度

### 事件调用的顺序
通过对象属性或HTML属性注册的事件 => 使用addEventListenner注册的处理程序顺序调用 => 使用attachEvent注册的处理程序按任意顺序调用
### 事件委托
1. 一个经典的例子是取快递，公司里三名员工取快递有两种方案，一是三个人守在公司门口等快递上门，而是拜托前台小姐姐统一代收然后再根据姓名等信息给到他们手里。方案二的好处是前台小姐姐不仅可以代收原有的快递而且能代收新来员工的快递。  
2. 对应到coding中就是当元素的个数比较多时，可以委托他们的父元素代替执行事件。即使有新增加的元素也能确保触发相应事件。通过e.currentTarget.innerText || e.currentTarget.innerText可以获取点击子元素的文本信息。  
3. 如果被遮挡，比如ul > li > div > p,div，p和li一样，那么可以通过判断当前的元素是否是li元素，不是则取它的parentNode再进行判断。  
4. 适合使用事件委托的事件有click,mousedown,mouseup,keydown,keyup等。mousemove不适合是因为需要经常计算它们的位置处理不容易。blur和focus等不具备冒泡能力。
5. currentTarget和target都会触发冒泡事件，但前者指向事件绑定的元素，后者指向触发事件的元素

### 防抖和节流
#### 防抖
在事件触发的规定一段时间后再执行，如果未达到规定时间再次触发事件则以新触发事件的事件为准隔n秒后再执行。

![image](http://jiangliuer.vip/download/image/debounce.gif)  
#### 节流
持续触发事件，每隔一段时间，只执行一次事件。  
![image](http://jiangliuer.vip/download/image/throttle.gif)  

### 垃圾回收机制
#### 引用计数
实时计算一个变量的引用次数，当引用次数达到零时销毁。
![image](http://jiangliuer.vip/download/image/GC1.jpg)
好处是能够实时回收不再引用的变量的内存，最大的缺点是当两个变量互相引用时会造成内存泄漏

#### 标记清除
GC从全局作用域中的变量，沿作用域向里变量，也就是深度优先遍历。遍历到堆中的变量时打上一个标记，然后再递归遍历该对象所引用的对象，直到最后一个节点。
遍历堆中对象，清除没有标记的对象，释放其内存。
![image](http://jiangliuer.vip/download/image/GC2.jpg)  
实现简单，解决了循环引用的问题，但两次遍历较耗时，变量得不到及时回收。

### setTimeout、setInterval和requestAnimationFrame之间的区别
（1）requestAnimationFrame会把每一帧中的所有DOM操作集中起来，在一次重绘或回流中就完成，并且重绘或回流的时间间隔紧紧跟随浏览器的刷新频率。

（2）在隐藏或不可见的元素中，requestAnimationFrame将不会进行重绘或回流，这当然就意味着更少的CPU、GPU和内存使用量。

（3）requestAnimationFrame是由浏览器专门为动画提供的API，在运行时浏览器会自动优化方法的调用，并且如果页面不是激活状态下的话，动画会自动暂停，有效节省了CPU开销。
### event loop
1. 所有同步任务都在主线程上执行，形成一个执行栈。
2. 当主线程中的执行栈为空时，检查事件队列是否为空，如果为空，则继续检查；如不为空，则执行3；
3. 取出任务队列的首部，压入执行栈；
4. 执行任务；
5. 检查执行栈，如果执行栈为空，则跳回第 2 步；如不为空，则继续检查；
#### 每一次Event Loop触发时：

1. 执行完主执行线程中的任务也就是执行第一个macro-task任务，例如script任务。
2. 取出micro-task中任务执行直到清空。
3. 取出macro-task中一个任务执行。
4. 取出micro-task中任务执行直到清空。
5. 重复3和4。
6. 常见的微任务process.nextTick，promise

###  `==`和`===`、以及Object.is的区别
`==`是值比较，`===`是类型和值的比较，Object.is同`===`，但Object.is中，+0 !== -0,NaN === NaN; 

###  js监听对象属性的改变
#### es5，缺点是不能监听不存在的属性
```javascript
Object.defineProperty(user,'name',{
set：function(key,value){
}
})
```
#### es6 弥补上述方法的不足
```javascript
var  user = new Proxy({}，{
set：function(target,key,value,receiver){
}
})
```
#### 数据类型分类
##### 基本数据类型和对象数据类型  

1. 基本数据类型  
    undefined,null,string,boolean,number,symbol,BigInt
2. 对象数据类型  
    array,function,date,regexp,error

##### 可拥有对象方法和不可拥有对象方法

1. 可拥有
    string,boolean,number,symbol,object
2. 不可拥有
    null,undefined

##### 可变和不可变

1. 可变
    对象属性和数组值
2. 不可变
    null,undefined,布尔，数字，字符串。字符串看起来是可以修改的但实际调用字符串方法对字符串进行修改时并不会修改原始字符串而是返回一个修改后新的字符串。

#### 有趣的数字
##### 溢出

与其他语言一样，js的数字类型也有自己的取值返回，超出这个范围称为溢出。用infinity表示正无穷大，-infinity表示负无穷大
```
0 / 0   //NaN
3 / 0   //infinity
infinity +(-,*,/) 3    //infinity
infinity +(*) infinity     //infinity
infinity -(/) infinity     //NaN
```

##### 下溢

当计算结果无比趋向0时，称为下溢。正方向趋向0返回0，负方向返回-0.
```
0 === -0    //true
```

##### NaN  

表示非数字，与任何值都不相等包括其本身。可用isNaN()判断是否为NaN.

##### 浮点数比较的问题

js浮点数的表示方法也是采用的大多数语言的二进制表示法（IEEE-754）,因此会出现如下问题
```
.4 - .3 === .3 - .2     //false
.4 + .1 === .2 + .3     //true
```

#### null和undefined的关系和区别
都是对空值的描述，但null倾向于正常的，意料中的空值而undefined则倾向于是异常的，意料之外的空值。
```
null == undefined   //true
null === undefined  //false
typeof null     //"object"
typeof undefined    //"undefined"
null + ''   //"null"
undefined + ''  //"undefined"
null + 0    //0
undefined + 0  //NaN
```

#### 类型转换
##### 包装对象  
数值，字符串，布尔值都是非对象类型但看上去却具有对象所具有的属性和方法。以字符串为例，当调用字符串的toStirng()方法时字符串会调用new String()创造一个临时对象，该对象继承了字符串的原型方法，然后调用该临时对象的toString()方法。一旦调用结束，该临时变量会自动销毁。（实际并不一定创建和销毁这个临时变量，但整个过程看上去就是这样的）
##### 隐式类型转换  
使用运算符会导致数据类型的隐式转换
```
1 + ''      //'1'
'3' * '7'       //21
'a' * 1     //NaN
NaN + '233'     //"NaN233"
```
##### 一些特殊值的类型转换  
![image](http://jiangliuer.vip/download/image/typeChange.png)

#### 值传递和引用传递
##### 对象都是引用传递（地址），因此对象只和自身相等，而基本类型都是值传递。
```
//a重新赋值后指向了新的地址，因此与b脱离了关系，b指向a的原地址的值并没有发生变化
let a = [1,2,3];
b = a;
a = [4,5,6];
console.log(b);  //[1,2,3]
//不改变指向
let a = [1,2,3];
b = a;
a.pop();
console.log(b); //[1,2]
```


#### 判断类型的方法
##### typeof
```
typeof '1'  //"string"
typeof 1    //"number"
typeof Symbol('id')     //"symbol"
typeof true     //"boolean"
typeof null     //"object"
typeof undefined    //"undefined"
typeof []   //"object"
```
##### instanceof
```
'1' instanceof String  //false
1 instanceof Number  //false
Symbol('id') instanceof Symbol  //false
null instanceof Object  //false
undefined instanceof undefined  //抛出异常
true instanceof Boolean  //false
[] instanceof Array //true
```

##### Object.prototype.toString.call()
```
Object.prototype.toString.call('1') //"[object String]"
Object.prototype.toString.call(1)   //"[object Number]"
Object.prototype.toString.call(Symbol('id'))    //"[object Symbol]"
Object.prototype.toString.call(true)    //"[object Boolean]"
Object.prototype.toString.call(null)    //"[object Null]"
Object.prototype.toString.call(undefined)   //"[object Undefined]"
Object.prototype.toString.call([])  //"[object Array]"
```

### JSON.stringify
[more](https://juejin.im/post/5decf09de51d45584d238319#heading-8)
1. 函数，symbol，undefined作为对象属性时，将跳过它们的序列化
2. 函数，symbol，undefined作为数组时，序列化的结果是undefined
3. 函数，symbol，undefined作为单个值，序列化的结果是'null'，NaN，INfinity作为‘null’
4. 对象内部有toJSON函数，则序列化的结果是该函数的返回值
5. 包装对象new String('233')序列化结果为其原始值‘233’
6. 第二个参数作为函数可以实现类似数组map的方法
7. 深拷贝
```
function deepClone(obj){
    return JSON.parse(JSON.stringify(obj,(key,value) => {
        switch(typeof value){
            case "undefined": 
                return "undefined"
            case "symbol":
                return value.toString();
            case"function":
                return value.toString();
            default:
                break;
        }
      return value;
    }));
}
```

### for in 和for of的区别
[more](https://www.jianshu.com/p/c43f418d6bf0)
1. for in用于遍历对象，for of用于遍历数组
2. for in不用于遍历数组的原因是它会遍历数组上所有可枚举的属性，包括原型。

### js原型和原型链
js原型是一个容易混淆的概念。接下来我从三个属性来讲解js的原型以及原型链。  
#### `__proto__`: 
对象所特有的属性，指向创建该对象构造函数的原型，它是由一个对象指向另一个对象。当我们访问一个对象的某个属性时，首先会在该对象实例上寻找该属性，如果没有找到则会去该对象`__proto__`所指向的对象上寻找，依次类推直到到达顶端的null。以上不断通过`__proto__`属性来连接对象直到顶端的null值形成的一条链表就成为原型链。
#### prototype: 
函数所特有的属性，指向函数的原型对象。它是由一个函数指向一个对象，当然函数本身也是一个特有的对象。该属性的作用是创建一个特定类型的所有实例可继承的公共属性和公共方法，任何函数在创建的同时也会默认创建一个prototype属性。原型链的终点是Object.prototype?null。
#### constructor: 
对象所特有的属性，指向对象的构造函数。它是由一个对象指向一个函数。constructor的终点是Function，所有的对象和函数都是由Function构造而来。
```
let Foo = function(){}
let f1 = new Foo()
```
![image](http://jiangliuer.vip/download/image/proto.png)
- 练习题
```js
function C1(name) {
        if (name) {
            this.name = name;
        }
    }

function C2(name) {
        this.name = name;
    }

function C3(name) {
        this.name = name || 'join';
    }
C1.prototype.name = 'Tom';
C2.prototype.name = 'Tom';
C3.prototype.name = 'Tom';
alert((new C1().name) + (new C2().name) + (new C3().name));


 function Fn(num) {
        this.x = this.y = num;
    }
    Fn.prototype = {
        x: 20,
        sum: function () {
            console.log(this.x + this.y);
        }
    };
    let f = new Fn(10);
    console.log(f.sum === Fn.prototype.sum);
    f.sum();
    Fn.prototype.sum();
    console.log(f.constructor);


  function Fn() {
        this.x = 100;
        this.y = 200;
        this.getX = function () {
            console.log(this.x);
        }
    }
    Fn.prototype = {
        y: 400,
        getX: function () {
            console.log(this.x);
        },
        getY: function () {
            console.log(this.y);
        },
        sum: function () {
            console.log(this.x + this.y);
        }
    };
    var f1 = new Fn;
    var f2 = new Fn;
    console.log(f1.getX === f2.getX);
    console.log(f1.getY === f2.getY);
    console.log(f1.__proto__.getY === Fn.prototype.getY);
    console.log(f1.__proto__.getX === f2.getX);
    console.log(f1.getX === Fn.prototype.getX); 
    console.log(Fn.prototype.__proto__.constructor);
  
    var print=function(){alert(1);}
    function Fn() {
        print=function(){alert(2);}
        return this;
    }
    function print(){alert(3);}
    Fn.prototype.print=function(){alert(4);}
    Fn.print=function(){alert(5);}
    
    print();
    Fn.print();
    Fn().print();
    new Fn.print();
    new Fn().print();
```

### new操作符
new一个对象时，实际运行步骤如下.  
1. 创建一个新的空对象
2. 将新对象的`__proto__`属性指向构造函数的原型
3. 将构造函数内部的this绑定新建的这个对象上，然后调用构造函数对该对象进行初始化（类似于在调用了这个对象的constructor方法）
4. 如果构造函数没有返回原始值（引用类型的值），则默认返回this值也就是新建的这个对象，否则返回构造函数显式返回原始值。
```javascript
function myNew(constrc, ...args) {
	const obj = {}; // 1. 创建一个空对象
	obj.__proto__ = constrc.prototype; // 2. 将obj的[[prototype]]属性指向构造函数的原型对象
	constrc.apply(obj, args); // 3.将constrc执行的上下文this绑定到obj上，并执行
	return obj;  //4. 返回新创建的对象
}

// 使用的例子：
function Person(name, age){
	this.name = name;
	this.age = age;
}
const person1 = myNew(Person, 'Tom', 20)
console.log(person1)  // Person {name: "Tom", age: 20}

```


### js闭包
1. 闭包是指有权访问其他函数内部变量的函数
2. 常见的闭包是在一个函数中创建另一个函数
3. 闭包的特点是可以引用外层函数的参数，变量，不会被ie的gc主动回收内存
4. 闭包可以读取函数内部变量，也可以让这些变量始终保持在内存中
```javascript
function fun(n,o){
  console.log(o);
  return {
    fun: function(m){
      return fun(m,n);
    }
  };
}

var a = fun(0);  // ?
a.fun(1);        // ?        
a.fun(2);        // ?
a.fun(3);        // ?

var b = fun(0).fun(1).fun(2).fun(3);  // ?

var c = fun(0).fun(1);  // ?
c.fun(2);        // ?
c.fun(3);        // ?

```

### this指向
this的指向在函数创建的时候是确定不了的，只有函数被调用的时候才能真正地确立下来。this指向的对象有三种情况。
1. 一个函数有this，但它没有被上级函数调用，非严格模式下它指向window，严格模式下为undefined
2. 一个函数有this，且它被上一级对象调用，那么this指向的是上一级对象
3. 一个函数有this，这个函数中包含多个对象，尽管函数被最外层对象调用，但this还是会指向它的上一级对象
4. 构造函数中调用this，this指向新创建的对象
5. 隐式调用，如通过call，apply等方法调用，指向的是传入的对象。
```javascript
function a(){
    var user = "追梦子";
    console.log(this.user); //undefined
    console.log(this); //Window
}
a();    //window.a()

var o = {
    user:"追梦子",
    fn:function(){
        console.log(this.user);  //追梦子
    }
}
o.fn();

var o = {
    user:"追梦子",
    fn:function(){
        console.log(this.user); //追梦子
    }
}
window.o.fn();

//这里的this看起来也是指向上级对象但实际指向的是window，原因是使用了赋值表达式并没有直接执行，最终执行时指向的对象变味了window
var o = {
    a:10,
    b:{
        a:12,
        fn:function(){
            console.log(this.a); //undefined
            console.log(this); //window
        }
    }
}
var j = o.b.fn;
j();
```
### async和await
- async默认会返回一个promise
- async return 的值为promise.then中的值
- async函数中抛出的异常会在promise.catch中被捕获
- await a;await b;是继发执行   await promise.all[a,b]是并发执行

### bind，call，apply
#### call
```js
Function.prototype.mycall = function(ctx,...args){
    ctx = ctx || window;
    ctx.fn = this;
    let result = ctx.fn(...args);
    delete ctx.fn;
    return result;
}
```
#### apply
```js
Function.prototype.myapply = function(ctx,args){
    ctx = ctx || window;
    ctx.fn = this;
    let result;
    if(args){
         result = ctx.fn(args);
    }else{
         result = ctx.fn();
    }
    delete ctx.fn;
    return result;
}
```
#### bind
- a.bind(b).bind(c).bind(window)(12),那么每个对象有一个name,a的作用是打印name，最终的打印结果是？
- 结果是b.name.因为bind内部机制是将传入的对象作为执行上下文绑定到函数上返回一个绑定后的新函数。a.bind(b)之后a()它的上下文对象为b，之后的bind之所以失效是因为它们绑定的是上一个bind返回的函数并不是函数a
```js
Function.prototype.my_bind = function(...args) {
    let self = this, // 保存原函数
        context = args[0];
    return function(...arg) { // 返回一个新函数
        self.apply(context,[].concat.call(args.slice(1), arg));
    }
};

  function a(m, n, o) {
    console.log(this.name + ' ' + m + ' ' + n + ' ' + o);
  }

  var b = {
    name: 'kong'
  };

  a.my_bind(b, 7, 8)(9); // kong 7 8 9
    
```
### 继承
#### 原型链
优点：子类的实例也是父类的实例
缺点：无法实现多继承
```js
// 原型链继承
        function Person(){
            this.name = 'xiaopao';
        }

        Person.prototype.getName = function(){
            console.log(this.name);
        }

        function Child(){
            
        }

        Child.prototype = new Person();
        var child1 = new Child();
        child1.getName(); // xiaopao
```
#### 构造函数
优点：可以多继承
缺点：知识子类的实例不是父类的实例
```
// 借用构造函数继承（经典继承）
        function Person(){
            this.name = 'xiaopao';
            this.colors = ['red', 'blue', 'green'];
        }

        Person.prototype.getName = function(){
            console.log(this.name);
        }

        function Child(){
            Person.call(this);
        }

        var child1 = new Child();
        var child2 = new Child();
        child1.colors.push('yellow');
        console.log(child1.name);
        console.log(child1.colors); // ["red", "blue", "green", "yellow"]
        console.log(child2.colors); // ["red", "blue", "green"]
```
#### 组合继承
优点：结合前两点的有点
缺点：调用了两次父类构造函数
```
function father(name){
    this.name = name;
    this.sex = '男';
}
father.prototype.sayMyname = function(){
    console.log(this.name);
    return this.name;
};

function son(name,age) {
    father.call(this,name);
    this.age = age;
}

son.prototype = new father();
son.prototype.constructor = son;
```
#### 寄生式继承
新建一个父类对象的实例然后增强对象后返回该对象
```
        // 寄生式继承  可以理解为在原型式继承的基础上增加一些函数或属性
        var ob = {
            name: 'xiaopao',
            friends: ['lulu','huahua']
        }

        function CreateObj(o){
            function F(){};  // 创建一个构造函数F
            F.prototype = o;
            return new F();
        }

        // 上面CreateObj函数 在ECMAScript5 有了一新的规范写法，Object.create(ob) 效果是一样的 , 看下面代码
        var ob1 = CreateObj(ob);
        var ob2 = Object.create(ob);
        console.log(ob1.name); // xiaopao
        console.log(ob2.name); // xiaopao

        function CreateOb(o){
            var newob = CreateObj(o); // 创建对象 或者用 var newob = Object.create(ob)
            newob.sayName = function(){ // 增强对象
                console.log(this.name);
            }
            return newob; // 指定对象
        }

        var p1 = CreateOb(ob);
        p1.sayName(); // xiaopao 
```

#### 寄生组合式继承
当下最优的继承范式
```js
// 寄生组合式继承
  function Super(foo) {
    this.foo = foo
  }
  Super.prototype.printFoo = function() {
    console.log(this.foo)
  }
  function Sub(bar) {
    this.bar = bar
    Super.call(this)
  }
  Sub.prototype = Object.create(Super.prototype)
  Sub.prototype.constructor = Sub
```

### 私有属性
#### es5
```js
{
    //ES5
    var pet = {
        name:'小白ES5',
        age:10
    }
    Object.defineProperty(pet,'type',{
        writable:false,
        value:'dog'
    })
    console.table({
        name:pet.name,
        type:pet.type,
        age:pet.age,
    })
    pet.age = 12
    try{
        pet.type = 'cat'
    }catch(e){
        console.log(e)//在这里打印了由于给type设值抛出的错误
    }
    console.table({
        name:pet.name,
        type:pet.type,
        age:pet.age,
    })
}
```
#### es6
```js
{
    //ES6
    let pet = new Proxy(
    {
        name:'小白ES6',
        type:'dog',
        age:10
    },
    {
        get(target,key){
            return target[key]
        },
        set(target,key,val){
            if(key!=='type'){
                target[key] = val
                return true
            }
            return false
        }
    })
    console.table(pet)
    pet.age = 12
    try{
        pet.type = 'cat'
    }catch(e){
        console.log(e)//在这里打印了由于给type设值抛出的错误
    }
    console.table(pet)
}
```
### 模块化
#### CommonJS
主要用在Node开发上，每个文件就是一个模块，有一个模块作用域，通过module.export向外暴露接口。  
require函数在加载模块是同步的,然而CommonJS模块的加载机制局限了CommonJS在客户端上的使用，因为通过HTTP同步加载CommonJS模块是非常耗时的。

#### AMD
AMD规范的被依赖模块是异步加载的，而定义的模块是被当作回调函数来执行的，依赖于require.js模块管理工具库。
#### CMD
CMD 是 SeaJS 在推广过程中对模块定义的规范化产出。AMD 推崇依赖前置，CMD 推崇依赖就近。CMD集成了CommonJS和AMD的的特点，支持同步和异步加载模块。CMD加载完某个依赖模块后并不执行，只是下载而已，在所有依赖模块加载完成后进入主逻辑，遇到require语句的时候才执行对应的模块，这样模块的执行顺序和书写顺序是完全一致的。
#### ES6
模块化规范输出的是一个值的拷贝，ES6 模块输出的是值的引用。 
es6编译时执行，node运行时加载。

es6可以输出多个模块，node只能输出一个对象。

### JS执行上下文栈和作用域链
1. 执行上下文就是当前 JavaScript 代码被解析和执行时所在环境, 
    1. JS执行上下文栈可以认为是一个存储函数调用的栈结构，遵循先进后出的原则。

    2. JavaScript执行在单线程上，所有的代码都是排队执行。
        一开始浏览器执行全局的代码时，首先创建全局的执行上下文，压入执行栈的顶部。
    3. 每当进入一个函数的执行就会创建函数的执行上下文，并且把它压入执行栈的顶部。当前函数执行-完成后，当前函数的执行上下文出栈，并等待垃圾回收。
    4. 浏览器的JS执行引擎总是访问栈顶的执行上下文。
    5. 全局上下文只有唯一的一个，它在浏览器关闭时出栈。
2. 作用域链: 无论是 LHS 还是 RHS 查询，都会在当前的作用域开始查找，如果没有找到，就会向上级作用域继续查找目标标识符，每次上升一个作用域，一直到全局作用域为止。

### js中栈和堆的区别

在js中执行栈中存放函数的参数值，局部变量，对象的指针等，在函数执行完毕释放

堆则是存放对象的数据结构，通过垃圾回收机制释放内存