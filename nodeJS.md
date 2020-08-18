## 模块化

### quick start

`nodeJS`模块化主要依赖于`CommonJS`规范

- 定义一个模块

```js
module.exports = {
  motto: '如果有来生要做一棵树，站成永恒，没有悲欢的姿态'
}
```

- 引入一个·模块

```js
const koa = require('koa');
```

### 模块是怎么引入加载的？

首先在`Node`中初次引入模块，需要经历下面三个步骤。值得注意的是`Node`会自动对引用过的模块进行缓存，加载时也是优先从缓存中加载。

1. 路径分析
2. 文件定位
3. 编译执行

#### 路径分析

根据不同的模块类型，`Node`会采用不同的分析方式

- **核心模块**：`Node`内置的模块，加载优先级仅次于缓存，由于部分模块在`Node`编译时就已经转换为二进制代码了，因此加载速度最快。

- **路径形式的文件模块**：以`. | .. | /`开始的标识符都会被作为文件模块进行处理。分析路径模块时，`require()`方法会将其路径转为真实路径（即绝对路径），并以真实路径为索引，将编译后的结果存放到缓存中，使其二次加载时更快。

- **自定义模块**：查找最费时，加载最慢，分析过程类似于原型链查找的过程。

  **当前文件目录下的node_module**  => **父级文件目录下的node_module** => ... => **根目录下的node_module**

#### 文件定位

- **扩展名的分析：**`require()`加载模块时允许不包含文件扩展名，它会按照`.js | .json | .node`的顺序进行补全
- **分析目录和包**：`Node`会根据`package.json`中的配置信息`main属性`进行文件定位，如果不存在`package.json`，`Node`会将`index`作为文件名，并依次查找`index.js | index.json | index.node`

#### 模块编译

- **.js文件：**通过`fs`模块同步读取文件后编译执行
- **.node文件：**`C/C++`编写的扩展文件，通过`dlopen()`方法加载后编译生成的文件
- **.json文件：**通过`fs`模块同步读取文件后，使用`JSON.parse（）`解析后返回结果

#### 实现一个`Node`包

https://juejin.im/post/5bdfa46951882516c94e5b26

#### NPM是什么

`npm`全称`node package manager`，顾名思义，它是`node`包的管理工具。

**作为一个装包神器，以下是一些常用命令**

```js
// 初始化一个node包
npm init

// 下载node包
npm install packageName
// 下载最新版
npm install packageName@latest
// 下载指定版本
npm install packageName@1.0

// 卸载node包
npm uninstall packageName

// 设置node下载源
npm set registry https://registry.npm.taobao.org

// 获取当前node下载源
npm get registry

// 更好的方案
请使用yarn吧，颜值高，装包快，不容易丢包🚀
```

## 异步I/O

### 为什么异步I/O

传统后端语言更多采用多线程的设计模式，可以充分地利于多核处理器的资源并且可以并行地执行任务，但是创建线程和线程上下文切换的开销以及多线程编程面临的锁和状态同步问题是的开发的成本较高。

而`node`利于单线程，远离多线程死锁、状态同步等问题。利用异步I/O，让单线程远离阻塞，以更好的使用CPU

### 阻塞I/O和非阻塞I/O

- `OS`中对于`I/O`只有两种方式：阻塞和非阻塞。前者一定要等到系统内核层面完成所有操作后，调用才结束。比如读取文件时，系统内核在完成磁盘寻道、读取数据、复制数据到内存中之后，这个调用才会结束。

- 而非阻塞`I/O`会在完成调用操作后立即返回（即不需要等待磁盘寻道、读取数据、复制数据到内存这些过程）
- 但是非阻塞`I/O`想要获取数据则需要重复调用`I/O`操作来获取返回的数据，也就是轮询。
- `epoll`轮询方案是效率最高的轮询方案，它在轮询时如果没有检测到`I/O`事件就会进行休眠，知道事件发生将其唤醒。
- `node`通过线程池实现非阻塞异步`I/O`，通过让部分线程进行轮询以便获取数据，让一个线程进行计算处理，通过线程之间的通信将`I/O`得到的数据进行传递。
-  `node`通过`libuv`库实现在`*nix`和`window`平台上的异步`I/O`

#### Node中的异步I/O

#### 事件循环

其中 libuv 引擎中的事件循环分为 6 个阶段，它们会按照顺序反复运行。每当进入某一个阶段的时候，都会从对应的回调队列中取出函数去执行。当队列为空或者执行的回调函数数量到达系统设定的阈值，就会进入下一阶段。

![](https://camo.githubusercontent.com/992acfd5750f98c9a56a9a96e95111bdf7cc7669/68747470733a2f2f757365722d676f6c642d63646e2e786974752e696f2f323031392f312f31322f313638343162643938363063316565393f773d33353926683d33333126663d706e6726733d3130353037)

从上图中，大致看出 node 中的事件循环的顺序：

外部输入数据-->轮询阶段(poll)-->检查阶段(check)-->关闭事件回调阶段(close callback)-->定时器检测阶段(timer)-->I/O 事件回调阶段(I/O callbacks)-->闲置阶段(idle, prepare)-->轮询阶段（按照该顺序反复运行）...

- timers 阶段：这个阶段执行 timer（setTimeout、setInterval）的回调
- I/O callbacks 阶段：处理一些上一轮循环中的少数未执行的 I/O 回调
- idle, prepare 阶段：仅 node 内部使用
- poll 阶段：获取新的 I/O 事件, 适当的条件下 node 将阻塞在这里
- check 阶段：执行 setImmediate() 的回调
- close callbacks 阶段：执行 socket 的 close 事件回调

注意：**上面六个阶段都不包括 process.nextTick()**(下文会介绍)

接下去我们详细介绍`timers`、`poll`、`check`这 3 个阶段，因为日常开发中的绝大部分异步任务都是在这 3 个阶段处理的。

##### timer

timers 阶段会执行 setTimeout 和 setInterval 回调，并且是由 poll 阶段控制的。  
同样，**在 Node 中定时器指定的时间也不是准确时间，只能是尽快执行**。

##### poll

poll 是一个至关重要的阶段，这一阶段中，系统会做两件事情

1.回到 timer 阶段执行回调

2.执行 I/O 回调

并且在进入该阶段时如果没有设定了 timer 的话，会发生以下两件事情

- 如果 poll 队列不为空，会遍历回调队列并同步执行，直到队列为空或者达到系统限制
- 如果 poll 队列为空时，会有两件事发生
  - 如果有 setImmediate 回调需要执行，poll 阶段会停止并且进入到 check 阶段执行回调
  - 如果没有 setImmediate 回调需要执行，会等待回调被加入到队列中并立即执行回调，这里同样会有个超时时间设置防止一直等待下去

当然设定了 timer 的话且 poll 队列为空，则会判断是否有 timer 超时，如果有的话会回到 timer 阶段执行回调。

##### check 阶段

setImmediate()的回调会被加入 check 队列中，从 event loop 的阶段图可以知道，check 阶段的执行顺序在 poll 阶段之后。  
我们先来看个例子:

```js
console.log('start')
setTimeout(() => {
  console.log('timer1')
  Promise.resolve().then(function() {
    console.log('promise1')
  })
}, 0)
setTimeout(() => {
  console.log('timer2')
  Promise.resolve().then(function() {
    console.log('promise2')
  })
}, 0)
Promise.resolve().then(function() {
  console.log('promise3')
})
console.log('end')
//start=>end=>promise3=>timer1=>timer2=>promise1=>promise2
```

- 一开始执行栈的同步任务（这属于宏任务）执行完毕后（依次打印出 start end，并将 2 个 timer 依次放入 timer 队列）,会先去执行微任务（**这点跟浏览器端的一样**），所以打印出 promise3
- 然后进入 timers 阶段，执行 timer1 的回调函数，打印 timer1，并将 promise.then 回调放入 microtask 队列，同样的步骤执行 timer2，打印 timer2；这点跟浏览器端相差比较大，**timers 阶段有几个 setTimeout/setInterval 都会依次执行**，并不像浏览器端，每执行一个宏任务后就去执行一个微任务（关于 Node 与浏览器的 Event Loop 差异，下文还会详细介绍）

#### 观察者

在`Node`中，事件主要来源于网络请求，文件`I/O`等，这些事件都有相应的观察者。

事件循环是一个典型的生产者/消费者模型。异步I/O、网络请求等是事件的生产者，事件被传递到对应的观察者那里，事件循环则从观察者那里取出事件并处理。

#### 请求对象

`JavaScript`发起调用到内核执行完`I/O`操作的过渡过程中，存在一种中间产物，被称为请求对象。也就是说回调函数并不是由开发者调用而是由请求对象进行调用。

1. `JS`调用`Node`核心模块
2. `Node Core`调用`C++`内建模块
3. 内建模块通过`libuv`进行系统调用。此时会生成一个请求对象，`JS`层传入的参数和方法都包装在这个请求对象中，包括回调函数（被设在`oncomplete`属性上）
4. 对象包装完成后，`Windows`平台会将对象推入线程池中等待执行。

#### 执行回调

1. 线程池中的`I/O`操作执行完毕后，会将获取到的结果存储在`req->result`属性上，然后通知`IOCP`(`windos`平台实现异步`I/O`的解决方案)，告知当前对象操作已完成
2. 此时会调用事件循环的`I/O`观察者，在每次`Tick`的执行中，他会调用`ICOP`相关的方法检测线程池中是否含有未执行完毕的请求。如果存在，会将请求对象加入到`I/O`观察者的队列中，然后将其当做事件处理。至此整个异步`I/O`操作到此结束

## 异步编程的解决方案

### 发布-订阅模式

```js
class EventEmitter{
    private events: Object = {};    // 存储事件
    private key: number = 0;    // 事件的唯一标识key

    on(name: string,event: any): number{
        event.key = ++this.key;
        this.events[name] ? this.events[name].push(event) 
                          : (this.events[name] = []) && this.events[name].push(event);
        return this;
    }

		once(name: string,cb){
      let cb = (...args) => {
        cb.call(this,...args);
        this.off(name);
      }
      this.on(name,cb);
      return this;
    }

    off(name: string,key?: number){
        if(this.events[name]){
            this.events[name] = this.events[name].filter(x => x.key !== key);
        }else{
            this.events[name] = [];
        }
      	return this;
    }

    emit(name: string,key?: number){
        if(this.events[name].length === 0 ) throw Error(`抱歉，你没有定义 ${name}监听器`)
        if(key){
            this.events[name].forEach(x => x.key === key && x());
        }else {
            this.events[name].forEach(x => x());
        }
      	return this;
    }
}
```

#### 雪崩问题

在高访问量、大并发量的情况下缓存失效的场景、此时大量的请求同时涌入数据库中，数据库无法承受如此大的查询请求，进而往前影响到网站整体的响应速度。

#### 使用哨兵保证事件的执行顺序

```js
// 使用偏函数
// 这里是一个按需加载的demo
let after = function(times, cb) {
  let count = 0,results = {};
  return function (key, value) {
    results[key] = value;
    count++;
    if (count === times) cb(results);
  }
}
const emitter = new events.Emitter(); 
let done = after(times, render); 
emitter.on("done", done); 
emitter.on("done", other); 
fs.readFile(template_path, "utf8", function (err, template) { 
 emitter.emit("done", "template", template); 
}); 
db.query(sql, function (err, data) { 
 emitter.emit("done", "data", data); 
}); 
l10n.get(function (err, resources) { 
 emitter.emit("done", "resources", resources); 
});
```

### Promise/Deferred模式

- `Promise.then`挂载回调函数
- 由`deferred`中的`resolve | reject`执行回调

```js
function myPromise(construc){
    let self = this;
    this.status = 'pending';
    this.value = undefined;
    this.reason = undefined;
    this.resolveQueue = [];
    this.rejectQueue = [];

    function resolve(value) {
        if(self.status === 'pending'){
            self.status = 'fulfilled';
            self.value = value;
            self.resolveQueue.forEach((fn)=>fn());
        }
    }

    function reject(reason) {
        if(self.status === 'pending'){
            self.status = 'rejected';
            self.reason = reason;
            self.rejectQueue.forEach((fn)=>fn());
        }
    }

    try {
        construc(resolve,reject);
    }catch (e) {
        reject(e);
    }
}

myPromise.prototype.then = function(res,rej){
    this.status === 'fulfilled' && res(this.value);
    this.status === 'rejected' && rej(this.reason);
    if(this.status === 'pending'){
        this.resolveQueue.push(()=>res(this.value));
        this.rejectQueue.push(()=>rej(this.reason));
    }
};

let p = new myPromise((res,rej) => {
    setTimeout(res(1),1000)
}).then((e) => console.log(e))
```

### async, await

```js
async function fn() {
  const a = await new Promise((res) => {
    res(1);
  })
  console.log(a);
}
// 1
```

## 内存控制

### V8垃圾回收机制

1. 对象分为新生代对象和老生代对象，新生代占用两个`semispace`，老生代占用较大空间的内存
2. 将堆内存一分为二，每一部分被称为`semispace`。在这两个`semispace`中一个处于使用中（`Form`），另一个处于闲置状态(`To`)
3. 分配对象时先是在`From`进行分配，当进行垃圾回收时会将`From`中的存活对象复制到`To`中，复制完成后对换`From`和`To`
4. 每次对换时，检查存活对象是否已经经历过`Scavenge`回收算法以及`To`空间占用是否大于25%，满足其中

一个条件即可完成新生代对象向老生代对象的晋升

5. 老生代采用标记清除和标记整理算法（针对标记清除的一种改进，主要是将活着的对象移向一端，移动完成后直接清理掉边界的内存）
6. 查看日志

```js
node projectName --trace_gc
```

### 内存指标

**查看进程的内存使用情况**

```js
// node进程的内存占用情况
process.memoryUsage()

// os的内存使用情况
os.totalmem()	// 总的内存使用	
os.freemem()	// 空闲的内存使用
```

### 内存泄漏

**主要原因**

- **缓存：**缓存中的内存不能得到释放，当缓存对象的体积越来越大时容易造成内存泄漏。解决方案有以下几点
  - 缓存限制策略，如`OS`中的先来先服务，`LRU`算法等，进行缓存的更迭
  - 将缓存转移到外部，减少常驻内存的对象的数量，让垃圾回收更加高效
  - 进程之间可以共享缓存
  - 了解一下`redis`
- **队列消费不及时:**`Task`队列中，消费速度低于生产速度，造成 内存对象的堆积，可能造成内存泄漏。解决方案如下
  - 设置一个监控系统，当队列堆积时通知相关人员
  - 设置一个超时机制，调用加入到队列中就开始计时，超时就直接响应一个超时错误
- **作用域未释放：**如闭包变量，一些全局变量未及时释放空间造成的内存泄漏。

**排查方法**

- **node-heapdump:**  https://github.com/bnoordhuis/node-heapdump
- **node-memwatch:**https://github.com/lloyd/node-memwatch

### 大内存应用

`Node`中采用`Stream`模块来读取和写入内存较大的应用

```js
const fs = require('fs');
let reader = fs.createReadStream('in.txt');
let writer = fs.createWriteStream('out.txt');

reader.on('data', function (chunk) { 
 writer.write(chunk); 
}); 
reader.on('end', function () { 
 writer.end(); 
});
```

## Buffer对象

`Buffer`对象类似于数组，它的元素为16进制的两位数，即0到255的数值。

```js
let str = 'i love javaScript';
let buffer = new Buffer(str,'utf-8');
console.log(buffer); // <Buffer 69 20 6c 6f 76 65 20 6a 61 76 61 53 63 72 69 70 74>
```

### 内存分配

#### 分配机制

`Node`采用了`slab`分配机制，所谓`slab`其实是一块申请好的固定大小的内存区域。具有如下3中状态

- **full:**完全分配状态
- **partial:**部分分配状态
- **empty:**没有被分配状态

#### 分配`Buffer`对象

- **分配小`Buffer`对象（小于8KB）**

  - 声明的`Buffer`对象内存占用小于`8KB`时，会生成一个中间对象`pool`；然后下次申请时，会查看`pool`中的内存空间是否足够，足够的话加入到该`pool`对象指向的`slab`内存单元，不足的话就重新创立一个`slab`单元，将其添加进去。
  - 整个过程看起来就像

  ```js
  function allocPool() { 
   pool = new SlowBuffer(Buffer.poolSize); 
   pool.used = 0; 
  }
  if (!pool || pool.length - pool.used < this.length) allocPool();
  ```

  - 值得注意的是：当第一次申请的`slab`单元未用完，第二次申请的单元又大于第一次申请所剩下的单元时，那些空闲的空间不能及时回收的话，就会造成浪费

- **分配大`Buffer`对象：**如果需要超过8KB的Buffer对象，将会直接分配一个`SlowBuffer`对象作为`slab`单元，这个`slab`单元将会被这个大的`Buffer`对象独占

#### 乱码问题

使用字符串拼接`buffer array`时，会按照限定的`Buffer`对象长度进行分割（默认11），而中文在`utf-8`中占3个字节，存在截断的问题，因此造成乱码。

对于任意长度的Buffer而言，宽字节字符串都有

可能存在被截断的情况，只不过Buffer的长度越大出现的概率越低而已，但该问题依然不可忽视。

拼接的示例代码

```js
Buffer.concat = function(list, length) { 
 if (!Array.isArray(list)) { 
   throw new Error('Usage: Buffer.concat(list, [length])'); 
 } 
 if (list.length === 0) { 
	 return new Buffer(0); 
 } else if (list.length === 1) { 
 	return list[0]; 
 } 
 if (typeof length !== 'number') { 
	 length = 0; 
	 for (let i = 0; i < list.length; i++) { 
		 length += list[i].length; 
	 } 
 } 
 const buffer = new Buffer(length); 
 let pos = 0; 
 for (var i = 0; i < list.length; i++) { 
 	 let buf = list[i]; 
	 buf.copy(buffer, pos); 
	 pos += buf.length; 
 } 
	 return buffer; 
};
```

### 性能考量

`Buffer`是二进制数据，相比字符串传输，其传输性能能达到字符串的两倍以上。但`Buffer`的使用细节需要多注意，不然很容易造成莫名的乱码和内存浪费的问题

## 网络编程

### 构建TCP服务

#### quick start

```js
// server.js
var net = require('net'); 
var server = net.createServer(function (socket) { 
 // 新的连接
 socket.on('data', function (data) { 
 socket.write("你好"); 
 }); 
 socket.on('end', function () { 
 console.log('连接断开'); 
 }); 
 socket.write("欢迎光临《深入浅出Node.js》示例：\n"); 
}); 
server.listen(8124, function () { 
 console.log('server bound'); 
});

// client.js
var net = require('net'); 
var client = net.connect({port: 8124}, function () { //'connect' listener 
 console.log('client connected'); 
 client.write('world!\r\n'); 
}); 
client.on('data', function (data) { 
 console.log(data.toString()); 
 client.end(); 
}); 
client.on('end', function () { 
 console.log('client disconnected'); 
});
```

#### api

- server event

  -  listening：在调用server.listen()绑定端口或者Domain Socket后触发，简洁写法为

    server.listen(port,listeningListener)，通过listen()方法的第二个参数传入。

  - connection：每个客户端套接字连接到服务器端时触发，简洁写法为通过net.create

    Server()，最后一个参数传递。

  - close：当服务器关闭时触发，在调用server.close()后，服务器将停止接受新的套接字

    连接，但保持当前存在的连接，等待所有连接都断开后，会触发该事件。

  - error：当服务器发生异常时，将会触发该事件。比如侦听一个使用中的端口，将会触发

    一个异常，如果不侦听error事件，服务器将会抛出异常。

- client event

  - data：当一端调用write()发送数据时，另一端会触发data事件，事件传递的数据即是

    write()发送的数据。

  - end：当连接中的任意一端发送了FIN数据时，将会触发该事件。

  - connect：该事件用于客户端，当套接字与服务器端连接成功时会被触发。

  - drain：当任意一端调用write()发送数据时，当前这端会触发该事件。

  - error：当异常发生时，触发该事件。

  - close：当套接字完全关闭时，触发该事件。

  - timeout：当一定时间后连接不再活跃时，该事件将会被触发，通知用户当前该连接已经

    被闲置了。

### 构建UDP服务

#### quick start

```js
// server.js
var dgram = require("dgram"); 
var server = dgram.createSocket("udp4"); 
server.on("message", function (msg, rinfo) { 
 console.log("server got: " + msg + " from " + 
 rinfo.address + ":" + rinfo.port); 
}); 
server.on("listening", function () { 
 var address = server.address(); 
 console.log("server listening " + 
 address.address + ":" + address.port); 
}); 
server.bind(41234);

// client.js
var dgram = require('dgram'); 
var message = new Buffer("深入浅出Node.js"); 
var client = dgram.createSocket("udp4"); 
client.send(message, 0, message.length, 41234, "localhost", function(err, bytes) { 
 client.close(); 
}); 
$ node server.js 
server listening 0.0.0.0:41234 
server got: 深入浅出Node.js from 127.0.0.1:58682
```

#### api

- message：当UDP套接字侦听网卡端口后，接收到消息时触发该事件，触发携带的数据为

消息Buffer对象和一个远程地址信息。

- listening：当UDP套接字开始侦听时触发该事件。

- close：调用close()方法时触发该事件，并不再触发message事件。如需再次触发message

事件，重新绑定即可。

- error：当异常发生时触发该事件，如果不侦听，异常将直接抛出，使进程退出。

### 构建HTTP服务

#### quick start

```js
var http = require('http'); 
http.createServer(function (req, res) { 
 res.writeHead(200, {'Content-Type': 'text/plain'}); 
 res.end('Hello World\n'); 
}).listen(1337, '127.0.0.1'); 
console.log('Server running at http://127.0.0.1:1337/');
```

#### api

- server

  - connection事件：在开始HTTP请求和响应前，客户端与服务器端需要建立底层的TCP连

  接，这个连接可能因为开启了keep-alive，可以在多次请求响应之间使用；当这个连接建

  立时，服务器触发一次connection事件。

  - request事件：建立TCP连接后，http模块底层将在数据流中抽象出HTTP请求和HTTP响

  应，当请求数据发送到服务器端，在解析出HTTP请求头后，将会触发该事件；在res.end()

  后，TCP连接可能将用于下一次请求响应。

  - close事件：与TCP服务器的行为一致，调用server.close()方法停止接受新的连接，当已

  有的连接都断开时，触发该事件；可以给server.close()传递一个回调函数来快速注册该

  事件。

  - checkContinue事件：某些客户端在发送较大的数据时，并不会将数据直接发送，而是先

  发送一个头部带Expect: 100-continue的请求到服务器，服务器将会触发checkContinue

  事件；如果没有为服务器监听这个事件，服务器将会自动响应客户端100 Continue的状态

  码，表示接受数据上传；如果不接受数据的较多时，响应客户端400 Bad Request拒绝客

  户端继续发送数据即可。需要注意的是，当该事件发生时不会触发request事件，两个事

  件之间互斥。当客户端收到100 Continue后重新发起请求时，才会触发request事件。

  - connect事件：当客户端发起CONNECT请求时触发，而发起CONNECT请求通常在HTTP代理时

  出现；如果不监听该事件，发起该请求的连接将会关闭。7.3 构建 HTTP 服务 161 

  - upgrade事件：当客户端要求升级连接的协议时，需要和服务器端协商，客户端会在请求

  头中带上Upgrade字段，服务器端会在接收到这样的请求时触发该事件。这在后文的

  WebSocket部分有详细流程的介绍。如果不监听该事件，发起该请求的连接将会关闭。

  - clientError事件：连接的客户端触发error事件时，这个错误会传递到服务器端，此时触

  发该事件。

- client

  - response：与服务器端的request事件对应的客户端在请求发出后得到服务器端响应时，

  会触发该事件。

  - socket：当底层连接池中建立的连接分配给当前请求对象时，触发该事件。

  - connect：当客户端向服务器端发起CONNECT请求时，如果服务器端响应了200状态码，客

  户端将会触发该事件。

  - upgrade：客户端向服务器端发起Upgrade请求时，如果服务器端响应了101 Switching 

  Protocols状态，客户端将会触发该事件。

  - continue：客户端向服务器端发起Expect: 100-continue头信息，以试图发送较大数据量，

  如果服务器端响应100 Continue状态，客户端将触发该事件。

### 构建Websocket服务

#### demo

```js
var WebSocket = function (url) { 
 // 伪代码，解析ws://127.0.0.1:12010/updates，用于请求
 this.options = parseUrl(url); 
 this.connect(); 
}; 
WebSocket.prototype.onopen = function () { 
 // TODO 
}; 
WebSocket.prototype.setSocket = function (socket) { 
 this.socket = socket; 
}; 
WebSocket.prototype.connect = function () { 
 var this = that; 
 var key = new Buffer(this.options.protocolVersion + '-' + Date.now()).toString('base64'); 
 var shasum = crypto.createHash('sha1'); 
 var expected = shasum.update(key + '258EAFA5-E914-47DA-95CA-C5AB0DC85B11').digest('base64'); 
 var options = { 
 port: this.options.port, // 12010 
 host: this.options.hostname, // 127.0.0.1
 headers: { 
 'Connection': 'Upgrade', 
 'Upgrade': 'websocket', 
 'Sec-WebSocket-Version': this.options.protocolVersion, 
 'Sec-WebSocket-Key': key 
 } 
 }; 
 var req = http.request(options); 
 req.end(); 
 req.on('upgrade', function(res, socket, upgradeHead) { 
 // 连接成功
 that.setSocket(socket); 
 // 触发open事件
 that.onopen(); 
 }); 
}; 
下面是服务器端的响应行为：
var server = http.createServer(function (req, res) { 
 res.writeHead(200, {'Content-Type': 'text/plain'}); 
 res.end('Hello World\n'); 
}); 
server.listen(12010); 
// 在收到upgrade请求后，告之客户端允许切换协议
server.on('upgrade', function (req, socket, upgradeHead) { 
 var head = new Buffer(upgradeHead.length); 
 upgradeHead.copy(head); 
 var key = req.headers['sec-websocket-key']; 
 var shasum = crypto.createHash('sha1'); 
 key = shasum.update(key + "258EAFA5-E914-47DA-95CA-C5AB0DC85B11").digest('base64'); 
 var headers = [ 
 'HTTP/1.1 101 Switching Protocols', 
 'Upgrade: websocket', 
 'Connection: Upgrade', 
 'Sec-WebSocket-Accept: ' + key, 
 'Sec-WebSocket-Protocol: ' + protocol 
 ]; 
 // 让数据立即发送
 socket.setNoDelay(true); 
 socket.write(headers.concat('', '').join('\r\n')); 
 // 建立服务器端WebSocket连接
 var websocket = new WebSocket(); 
 websocket.setSocket(socket); 
});
```

## 进程

### 创建子进程

|    类型    | 回调异常 | 进程类型 |  执行类型  | 可设置超时时间 |
| :--------: | :------: | :------: | :--------: | :------------: |
|  spawn()   |    ×     |   任意   |    命令    |       ×        |
|   exec()   |    √     |   任意   |    命令    |       √        |
| execFile() |    √     |   任意   | 可执行文件 |       √        |
|   fork()   |    ×     |   Node   | JavaScript |       ×        |

### 进程间通信

`Node`中的进程通信采用的是`pipe`的方式实现，其具体实现是由`libuv`提供，在应用层上的进程通信只有简单的`message`事件和`send`方法

#### 通过传句柄实现多个子进程监听同一个端口

```js
// parent.js
const child = require('child_process');
const child1 = child.fork('child.js');
const child2 = child.fork('child.js');

const server = require('net').createServer();
server.on('connection', function(socket) {
  child1.send('server', server);
  child2.send('server', server);
  server.close();
})

// child.js
const http = require('http');
const server = http.createServer(function(req,res) {
  res.writeHead(200, {'Content-type': 'text/plain'});
  res.end(process.pid);
})

process.on('message', function(m, tcp) {
  if(m === 'server') {
    tcp.on('connection', function(socket) {
      server.emit('connection', socket);
    })
  }
})
```

### 集群

#### 进程事件

- **message：**接收到信息时触发该事件
- **send：**发送信息的方法

- **error：**当子进程无法被复制创建、无法被杀死、无法发送消息时会触发该事件。

- **exit：**子进程退出时触发该事件，子进程如果是正常退出，这个事件的第一个参数为退出

码，否则为null。如果进程是通过kill()方法被杀死的，会得到第二个参数，它表示杀死

进程时的信号。

- **close：**在子进程的标准输入输出流中止时触发该事件，参数与exit相同。

- **disconnect：**在父进程或子进程中调用disconnect()方法时触发该事件，在调用该方法时

将关闭监听IPC通道。

#### 负载均衡

`Node`采用`Round-Robin`（轮叫调度）的策略实现负载均衡。

轮叫调度的工作方式是由主进程接受连接，将其依次分发给工作进程。分发的策略是在N个工作进程中，每次选择第`i=(i+1) mod n`个进程来发送连接。感觉就是普通的轮询

#### 状态共享

`Node`不允许多个进程之间共享数据，因此常用的数据共享的方式为：

- **第三方数据存储：**存储到数据库，缓存服务中等等
- **主动通知：**类似中介者模式，在数据改变时主动通知其他进程

#### `Cluster`模块

##### 创建子进程集群

```js
var cluster = require('cluster'); 
// 创建子进程
cluster.setupMaster({ 
 exec: "worker.js" 
}); 
var cpus = require('os').cpus(); 
for (var i = 0; i < cpus.length; i++) { 
 cluster.fork(); 
}
```

- fork：复制一个工作进程后触发该事件。

- online：复制好一个工作进程后，工作进程主动发送一条online消息给主进程，主进程收

到消息后，触发该事件。

- listening：工作进程中调用listen()（共享了服务器端Socket）后，发送一条listening

消息给主进程，主进程收到消息后，触发该事件。

- disconnect：主进程和工作进程之间IPC通道断开后会触发该事件。

- exit：有工作进程退出时触发该事件。

- setup：cluster.setupMaster()执行后触发该事件。