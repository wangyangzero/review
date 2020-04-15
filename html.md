### Doctype
用于告诉浏览器应该用哪种文档规范解析文档
1. 严格模式（标准模式),浏览器按照W3C标准来解析；
2. 混杂模式，向后兼容的解析方法，浏览器用自己的方式解析代码。

### 严格模式的限制
1. 变量和函数必须在声明后使用
2. 不允许使用with
3. 禁止this指向全局
4. 函数的参数不能有同名属性


### meta标签的作用
- name属性
    1. keyword
    2. description（seo）
    3. viewport
    4. robots(index,follow,all,noindex,nofollow,none)
    5. author

### src和href的区别
href指向网络上的资源，src将资源嵌入到当前文档中，在请求src资源时会将其指向的资源下载并应用到文档内。

### html5新特性
1. audio/video
2. web存储
3. web worker
4. 语义化标签
5. websocket

### html5 manifest

```html
<html manifest="url"/>
//url指向一个.appchche文件
//配置表
CACHE MANIFRST
# 离线存储的资源
NETWORK
# 需要联网加载的资源
FAILBACK
# 当前资源加载失败后的替换
url1 url2
```

原理：HTML5的离线存储是基于一个新建的.appcache文件的缓存机制(不是存储技术)，通过这个文件上的解析清单离线存储资源，这些资源就会像cookie一样被存储了下来。之后当网络在处于离线状态下时，浏览器会通过被离线存储的数据进行页面展示。

### 不改变图片原始大小画到canvas上

1. 直接在坐标上画图，如果图片大小超出了画布也不缩放 drawImage(image,x,y);
2. 绘制开始位置，缩放位置，图片会变形 drawImage(image,x1,y1,x2,y2);

#### 浏览器是怎么对HTML5的离线储存资源进行管理和加载的呢
  在线的情况下，浏览器发现html头部有manifest属性，它会请求manifest文件，如果是第一次访问app，那么浏览器就会根据manifest文件的内容下载相应的资源并且进行离线存储。如果已经访问过app并且资源已经离线存储了，那么浏览器就会使用离线的资源加载页面，然后浏览器会对比新的manifest文件与旧的manifest文件，如果文件没有发生改变，就不做任何操作，如果文件改变了，那么就会重新下载文件中的资源并进行离线存储。

### iframe的缺点
1. iframe会阻塞主页面的Onload事件
2. 搜索引擎的检索程序无法解读这种页面，不利于SEO
3. iframe和主页面共享连接池，而浏览器对相同域的连接有限制，所以会影响页面的并行加载
4. 使用iframe之前需要考虑这两个缺点。如果需要使用iframe，最好是通过javascript动态给iframe添加src属性值，这样可以绕开以上两个问题

### 全局属性
class,id,data-,style,title

### canvas和svg
1. svg绘制出来的每一个图形的元素都是独立的DOM节点，能够方便的绑定事件或用来修改。canvas输出的是一整幅画布
2. svg输出的图形是矢量图形，后期可以修改参数来自由放大缩小，不会失真和锯齿。而canvas输出标量画布，就像一张图片一样，放大会失真或者锯齿

### 常规浏览器内核
1. IE: trident内核
2. Firefox：gecko内核
3. Safari: webkit内核
4. Opera: 以前是presto内核，Opera现已改用Google - Chrome的Blink内核
5. Chrome: Blink(基于webkit，Google与Opera Software共同开发)

### ready和onload的区别

1. document.ready 是指文档加载好, dom 加载好，仅包含在它之前的 script 、css 标签 ， 但不包含 img 标签是否加载完成， 因为页面是按顺序执行的。

2. onload 是指页面完全加载好，包括所有节点，甚至 img 标签

### href和src

href是引用和页面关联，是在当前元素和引用资源之间建立联系，src表示引用资源，表示替换当前元素。

加载js用src，加载css用href。

### label

```
 label 标签来定义表单控制间的关系，当用户选择该标签时，浏览器会自动将焦点转到和标签相关的表单控件上。

 <label for="Name">Number:</label>
 <input type=“text“ name="Name" id="Name"/>
  <title> 定义文档的标题，它是 head 部分中唯一必需的元素。
```

####  attribute 和 property 的区别是什么？

前者是html属性，后者是js属性

#### disabled 和 readonly 的区别？

前者不会随表单提交，js脚本都可以修改value