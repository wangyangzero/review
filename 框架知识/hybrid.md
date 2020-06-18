## 哲学三问

### Hybrid是什么

`Hybrid`是开发app的一种架构设计模式，其核心是以Native作为底层容器，使用H5进行大部分页面开发，实现一套代码能够在三端上运行（Web浏览器端，Android，iOS）

### 怎么设计一个Hybrid架构

![image](https://raw.githubusercontent.com/chemdemo/chemdemo.github.io/master/img/hybrid/architecture.png)

hybrid主要依靠Native和H5进行开发，两者间的通信就是一个核心的问题，因而引申出了桥协议。

所谓桥协议本质上是基于接口进行信息交换的协议，其交互方式类似于JSONP的方式。

H5中有一个离线缓存的机制，该机制是实现Hybrid app像Native app一样在用户手机上运行地如丝般柔滑的关键。

既然使用了离线缓存，那么怎么对缓存进行更新，对于一些更新迭代快，存活周期不长的项目又该如何更新呢？

这就是H5 Container层设计的关键了，也是整个架构中最核心和最难的部分。

### 为什么要使用Hybrid

1. 一套代码三端使用，节约成本较Native低
2. 跨平台，兼容性好
3. 用户体验不错，更新速度快

## JSBridge

### 设计理念

![image](https://raw.githubusercontent.com/chemdemo/chemdemo.github.io/master/img/hybrid/jsbridge_1.png)

类比于HTTP请求，调用方将调用的`api 参数 请求签名（调用方产生）`携带上传给被调用方

被调用方处理完之后把结果以及请求签名回传给调用方

调用方再根据请求签名找到本次请求对应的回调函数并执行

### 通讯原理

- `JS`调用`Native`方法，一般是使用注入`API`的方式，通过`WebView`提供的接口，项`JavaScript`的`Context`中注入对象或者方法，让`JavaScript`调用时，直接执行相应的`Native`代码逻辑，达到`Js`调用`Native`的目的
- `Native`调用`JavaScript`则直接执行拼接好的`JavaScript`代码即可

#### H5调用Native

![image](https://raw.githubusercontent.com/chemdemo/chemdemo.github.io/master/img/hybrid/jsbridge_2.png)

#### Native调用H5

![image](https://raw.githubusercontent.com/chemdemo/chemdemo.github.io/master/img/hybrid/jsbridge_3.png)

### JSBridge接口实现

```js
1. 使用句柄标识Native方法或使用回调id标识相应句柄的回调函数
2. 调用Native接口上传数据，句柄 | 回调id
3. 接收到JS的消息，解析参数后根据句柄寻找相应的方法执行后以data作为参数并与句柄或callbackId等一起返回
4. 接收Native回传的数据，根据句柄或回调id执行相应的回调
5. Native也是类似的方法
(function () {
    var id = 0,
        callbacks = {};

    window.JSBridge = {
        // 调用 Native
        invoke: function(bridgeName, callback, data) {
            // 判断环境，获取不同的 nativeBridge
            var thisId = id ++; // 获取唯一 id
            callbacks[thisId] = callback; // 存储 Callback
            nativeBridge.postMessage({
                bridgeName: bridgeName,
                data: data || {},
                callbackId: thisId // 传到 Native 端
            });
        },
        receiveMessage: function(msg) {
            var bridgeName = msg.bridgeName,	// 使用句柄bridge标识native方法
                data = msg.data || {},
                callbackId = msg.callbackId; // Native 将 callbackId 原封不动传回
            // 具体逻辑
            // bridgeName 和 callbackId 不会同时存在
            if (callbackId) {
                if (callbacks[callbackId]) { // 找到相应句柄
                    callbacks[callbackId](msg.data); // 执行调用
                }
            } elseif (bridgeName) {

            }
        }
    };
})();

```

### 注入方式

- `Native`端进行注入，兼容性好，但在`JS`调用时需要对注入成功与否进行优先判断
- `JavaScript`端引用，`JS`端可以确定`JSBridge`的存在，但需要兼容多版本的`Native Bridge`

## 职责划分

### Native的作用场景

1. 关键界面、交互性强的界面
2. 导航组件
3. 系统级UI组件
4. 默认页面

### H5

Native以外的大多数页面

## 离线动态更新机制

![image](https://raw.githubusercontent.com/chemdemo/chemdemo.github.io/master/img/hybrid/workflow.png)

开发阶段，开发者可以通过HTTP代理方式直接访问开发机

完成开发之后，将H5代码推送到管理平台进行构建、打包

管理平台再通过事先设计好的长连接将新版本信息推送给客户端

客户端收到更新指令后开始下载新包、对包进行完整性校验，合并到本地的包，更新结束

## Local Url Router

H5的请求、线上和离线采用相同的URL进行访问，但是本地的相对路由地址并不是与URL一一对应的，因此需要对H5容器对H5资源请求进行拦截“映射”到本地，即`Local Url Router`

`Local Url Router`负责H5静态资源的分发（具体怎样分发由container层设计师决定），有一个不错的思路是：将映射的规则交给H5去生成，H5开发完毕后扫描一遍H5项目然后生成一份线上资源和离线资源路径的映射表，而H5容器只负责解析映射表。当H5容器拦截到静态资源请求时，如果本地有对应的文件则直接读取本地文件并返回，如果没有则发起一个HTTP请求获取线上资源同时另起一个线程去下载这个资源到本地。

## 当下跨平台架构比较

![image](https://pic4.zhimg.com/v2-e9174dc27c9fa4165038b1b3b201f27f_b.jpg)