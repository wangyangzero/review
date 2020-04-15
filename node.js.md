### node是什么

node.js是一个js的运行时，相比浏览器js少了dom和bom方法，增加了一些新的模块。node.js采用事件驱动，非阻塞式 I/O模型。

#### 事件驱动

当web server接收到请求，就把它关闭然后进行处理，然后去服务下一个web请求。当这个请求完成，它被放回处理队列，当到达队列开头，这个结果被返回给用户

#### 非阻塞I/O

如果有数据收到，就返回数据，如果没有数据收到，就立刻返回一个错误，如EWOULDBLOCK。这样是不会阻塞线程了，但是你还是要不断的轮询来读取或写入。

#### 事件循环

![image](https://user-gold-cdn.xitu.io/2019/1/12/16841bd9860c1ee9?imageslim)

外部输入数据=》轮询阶段=》检查阶段=》关闭回调=》定时器检测=》I/O回调阶段=》闲置

#### 浏览器和node事件循环的区别

##### 浏览器

![imafe](https://user-gold-cdn.xitu.io/2019/1/12/16841d6392e8f537?imageslim)

#### node

![image](https://user-gold-cdn.xitu.io/2019/1/12/16841d5f85468047?imageslim)

### koa 洋葱模型

![image](https://img2018.cnblogs.com/i-beta/1528420/202001/1528420-20200112165635180-708301499.png)

```js
const Koa = require('koa');
const app = new Koa();

// 中间件A
app.use(async (ctx, next) => {
  console.log('A获取画名字')
  await next()
  console.log('A给画表上画框')
})

// 中间件B
app.use(async ctx => {
  console.log('B获取画名字')
  console.log('B给画上色')
})

app.listen(3000)
```

