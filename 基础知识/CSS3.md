# 理论部分

## 概念

### 层叠

层叠的核心是`z-index`属性，该属性看起来是定义一个元素在屏幕Z轴上的堆叠顺序，`z-index`越大，离用户越近。但这个理解是片面的，实质上还要受限于以下条件：

1. `z-index`属性只会在`position: 非static`的情况下起作用
2. 判断元素在Z轴上的堆叠顺序，不仅仅是比较`z-index`的大小，而是由层叠上下文和层叠等级共同决定的

### 层叠上下文

#### 是什么

当两个或多个元素之间发生堆叠现象时，含有层叠上下文的元素会呈现到最上层（也就是离用户最近），覆盖掉下层元素

#### 怎么产生

1. HTML根元素本身就具有层叠上下文，称为`根层叠上下文`

2. 普通元素设置`position: 非static;z-index: 某值;`，就会产生层叠上下文

3. CSS3中的新属性也可以产生层叠上下文

   - 父元素的display属性值为`flex|inline-flex`，子元素`z-index`属性值不为`auto`的时候，子元素为层叠上下文元素；

   - 元素的`opacity`属性值不是`1`；

   - 元素的`transform`属性值不是`none`；

   - 元素`mix-blend-mode属性值不是`normal`；

   - 元素的`filter`属性值不是`none`；

   - 元素的`isolation`属性值是`isolate`；

   - `will-change`指定的属性值为上面任意一个；

   - 元素的`-webkit-overflow-scrolling`属性值设置为`touch`。

### 层叠等级计算

- 同一个层叠上下文中，层叠上下文元素在Z轴的排列顺序与`z-index`呈正比
- 不同层叠上下文中（即父元素节点不相同），那么父元素的`z-index`值越大，子元素节点的堆叠程度越高
- 同一层叠上下文中，`z-index`值相同，那么后面的元素的堆叠程度高于前面的元素

### 层叠顺序

![Snipaste_2020-05-28_16-06-10.png](https://i.loli.net/2020/05/28/XvmSidDsIptz1Ax.png)

### CSS如何阻塞文档解析

![image](https://segmentfault.com/img/remote/1460000018130508?w=624&h=289/view)

1. CSS加载不会影响DOM树的解析
2. CSS加载会阻塞DOM树的渲染
3. CSS加载会阻塞在其之后引入的JS语句的执行
4. CSS解析和HTML解析是一个并行的过程，但渲染需要在生成CSSOM树之后
5. 如果页面中同时存在css和js，并且js在css后面，那么DOMContentLoaded事件会在css加载完成后才执行
6. 其他情况下，DOMContentLoaded事件都不会等待css加载并且也不会等待其他静态资源的加载

### link和import的区别

link可以加载css以外的文件如预加载js文件，import只能加载css

link在页面载入时加载，import在页面载入完成后加载

link无兼容问题

link支持js控制dom改变样式

```html
<link as=script href='' rel=preload>
```

### CSS颜色体系

![image](https://user-gold-cdn.xitu.io/2018/6/7/163dabeee8cdb4e5?imageslim)

### CSS性能优化

#### 内联关键CSS代码

使用内联CSS能够更加快速地下载，从而提高页面的渲染速度。但也不是内联CSS越多越好，因为当CSS量很大时，在传输层中有一个拥塞窗口（限制单次传输流的大小），如果CSS文件太大需要进行拆分和组装，这显然会花费更多的时间，因此并不能达到减少页面渲染的目的。所以，**只将渲染首屏内容所需的关键CSS内联到HTML中**

#### 异步加载CSS

CSS会阻塞DOM的渲染，首屏的CSS是刚需，因而必须与DOM并行进行解析；而剩余的CSS可以通过外链的方式进行异步加载

```html
<link rel="preload" href="mystyles.css" as="style" onload="this.rel='stylesheet'">
```

#### 文件压缩

使用打包工具对CSS文件进行压缩，如`Webpack`

#### 编码考量

1. 有选择的使用选择器，尽可能保持扁平性，减少通配符的使用
2. 减少使用开销较大的属性，如`box-shadow | border-radius | filter | opacity | :nth-child ` 
3. 优化重排和重绘，减少布局改动，合理使用硬件加速等
4. 使用`link`替代`@import`
5. 去除无用的CSS代码

## 布局

### 盒模型

#### w3c盒模型
width标识内容宽度，块宽度 = width + paddding + border + margin
#### ie盒模型
width标识内容宽度 + padding + border，块宽度 = width + margin
#### box-sizing
1. content-box: w3c盒子
2. border-box: ie盒子
4. inherit: 继承父元素
#### inline，block，inline-block
1. 内联元素可以设置宽高但没用，margin左右起作用，padding都起作用
2. 块级元素宽高。。都有用，独占一行
3. 行内块元素，类似块元素，但不独占一行

#### img和background-image的区别
- 解析机制：img属于html标签，background-img属于css。img先解析
- SEO：img标签有一个alt 属性可以指定图像的替代文本，有利于SEO，并且在图片加载失败时有利于阅读
- 语义化角度：img语义更加明确

#### margin

margin基于父容器的width计算，垂直方向存在塌陷问题。

原因可能是父容器的高度可由子容器撑开，那么height就会动态变化，margin也会动态变化，导致布局的不稳定性或者说不准确。

### position

**固定定位fixed**： 

元素的位置相对于浏览器窗口是固定位置，即使窗口是滚动的它也不会移动。Fixed定位使元素的位置与文档流无关，因此不占据空间。 Fixed定位的元素和其他元素重叠。  

**相对定位relative**：  

如果对一个元素进行相对定位，它将出现在它所在的位置上。然后，可以通过设置垂直或水平位置，让这个元素“相对于”它的起点进行移动。 在使用相对定位时，无论是否进行移动，元素仍然占据原来的空间。因此，移动元素会导致它覆盖其它框。  

**绝对定位absolute**：  

绝对定位的元素的位置相对于最近的已定位`position: static`的父元素，如果元素没有已定位的父元素，那么它的位置相对于`<html>`。 absolute 定位使元素的位置与文档流无关，因此不占据空间。 absolute 定位的元素和其他元素重叠。  

**粘性定位sticky**：  

元素先按照普通文档流定位，然后相对于该元素在流中的flow root（BFC）和 containing block（最近的块级祖先元素）定位。而后，元素定位表现为在跨越特定阈值前为相对定位，之后为固定定位。  

**默认定位Static**：  

默认值。没有定位，元素出现在正常的流中（忽略top, bottom, left, right 或者 z-index 声明）。  

**inherit**:  

规定应该从父元素继承position 属性的值。

### rem布局

#### 换算关系

```
// 标记稿
width height
// 网页大小
W H
//换算
font-size: W / width;
```

#### 设置尺寸大小与屏幕宽度成比例的三种方案

1. 媒体查询

   ```css
   @media screen and (min-width:240px) {
       html, body, button, input, select, textarea {
           font-size:9px;
       }
   }
   @media screen and (min-width:320px) {
   	html, body, button, input, select, textarea {
   		font-size:12px;
   	}
   }
   // 红米Note2
   @media screen and (min-width:360px) {
   	html, body, button, input, select, textarea {
   		font-size:13.5px;
   	}
   }
   @media screen and (min-width:375px) {
   	html, body, button, input, select, textarea {
   		font-size:14.0625px;
   	}
   }
   ```

2. js设置html的font-size大小

   ```js
   @media screen and (min-width:240px) {
       html, body, button, input, select, textarea {
           font-size:9px;
       }
   }
   @media screen and (min-width:320px) {
   	html, body, button, input, select, textarea {
   		font-size:12px;
   	}
   }
   // 红米Note2
   @media screen and (min-width:360px) {
   	html, body, button, input, select, textarea {
   		font-size:13.5px;
   	}
   }
   @media screen and (min-width:375px) {
   	html, body, button, input, select, textarea {
   		font-size:14.0625px;
   	}
   }
   ```

3. 使用vw

   ```css
   html{
       font-size: 10vw;
   }
   ```

### flex兼容式写法

```css
.box{
    display: -webkit-flex;  /* 新版本语法: Chrome 21+ */
    display: flex;          /* 新版本语法: Opera 12.1, Firefox 22+ */
    display: -webkit-box;   /* 老版本语法: Safari, iOS, Android browser, older WebKit browsers. */
    display: -moz-box;      /* 老版本语法: Firefox (buggy) */
    display: -ms-flexbox;   /* 混合版本语法: IE 10 */   
 }
 
.flex1 {            
    -webkit-flex: 1;        /* Chrome */  
    -ms-flex: 1             /* IE 10 */  
    flex: 1;                /* NEW, Spec - Opera 12.1, Firefox 20+ */
    -webkit-box-flex: 1     /* OLD - iOS 6-, Safari 3.1-6 */  
    -moz-box-flex: 1;       /* OLD - Firefox 19- */       
}
```

### 使用过flex布局吗？flex-grow和flex-shrink属性有什么用？

1. flex-grow，当父控件还有剩余空间的时候，是否进行放大
2. flex-shrink，当父控件空间不够的时候，是否进行缩小
3. flex-basis，项目的长度auto，px，em，%
4. auto 11 auto none 00 auto flex:1 1 1 0% default: 0 1 auto

### gird网格布局

#### 网格布局基本概念

![Snipaste_2020-05-28_14-32-28.png](https://i.loli.net/2020/05/28/eXun1P7yvxQBlj8.png)

row line: 行线

column line: 列线

track: 网格轨道，即行线和行线，或列线和列线之间所形成的区域，用来摆放子元素

gap:  网格间距，行线和行线，或列线和列线之间所形成的不可利用的区域，用来分隔元素

cell: 网格单元格，由行线和列线所分隔出来的区域，用来摆放子元素

area: 网格区域，由单个或多个网格单元格组成，用来摆放子元素

#### 32原则

- **父容器**：定框架、设间隔、找对齐
- **子元素**：摆位置，找对齐

#### demo

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Grid</title>
</head>
<style>
.parent {
	display: grid;
	width: 500px;
	height: 500px;
	grid-template: 100px 100px 100px / 100px 100px 100px;
	grid-gap: 10px 10px;
    grid-template-areas: "a a b"
                         "c d e"
                         "c d i";
	justify-content: space-between;
	align-content: center;
}

/* 指定起始行，结束行，起始列，结束列 */
.child:first-child {
    grid-row-start: 1;
    grid-row-end: 2;
    grid-column-start: 1;
    grid-column-end: 3;
    background: red;
}

/* 使用缩写形式 */
.child:nth-child(2) {
	grid-row: 2/3;
	grid-column: 2/4;
	background: yellow;
}

/* 直接指定区域名 */
.child:nth-child(3) {
	grid-area: i;
	background: green;
}

</style>
<body>
    <div class="parent">
        <div class="child"></div>
        <div class="child"></div>
        <div class="child"></div>
    </div>
</body>
</html>
```

### bfc

margin重叠的计算方式：1.正，取最大 2. 不全正：正最大-绝对值最大 3. 负：取最小值

#### 创建一个bfc

1. position为absolute，flex
2. 根元素或其它包含它的元素
3. float不为none
4. display：inline-block，flex,table-cell
5. overflow为visible以外的值

#### bfc的范围

1. 一个元素不能同时存在于两个bfc中
2. 内部元素不会影响外部元素
3. bfc区域不与float box区域重合
4. 计算bfc高度，浮动元素也参与计算

### 隐藏元素

- opacity=0，该元素隐藏起来了，但不会改变页面布局，并且，如果该元素已经绑定一些事件，如click事件，那么点击该区域，也能触发点击事件的
- visibility=hidden，该元素隐藏起来了，但不会改变页面布局，但是不会触发该元素已经绑定的事件
- display=none，把元素隐藏起来，并且会改变页面布局，可以理解成在页面中把该元素删除掉一样。
- `<script><element></element></script>`，使用`script`包裹的元素不占用空间也不会渲染
- `position: relative;top: -100em`，将文本部分裁剪掉
- `<p style="text-indent: -999999px;">天下无双</p>`，用作隐藏文字

#### ifc

当块级元素内部只有内联元素时生效

1. 只计算横向空间不计算纵向
2. 设置text-align:center可以实现多元素水平居中
3. float元素优先排列

### 清除浮动的方法，overflow的原理

1. 设置一个带clear属性的空元素
2. 父元素设置浮动
3. 设置父元素的zoom为1,浮动元素设置overflow为hidden
4. display：inline-block也可以清除浮动
5. after伪元素

- overflow原理：构成bfc，内部只有一个浮动元素而浮动元素也会参与bfc容器高度计算，因此浮动就消失了

## 属性

### content属性

我们使用 content生成的文本是无法选中、无法复制的，好像设置了user-select:none声明一般，而普通元素的文本可以被轻松选中。

```css
基本使用
img:hover{
    content: url(laugh-tear.png);
}
```

### 文本溢出省略

- 单行
```css
overflow:hidden;
text-overflow: ellipsis;
white-space: nowrap;
```
- 多行
```css
overflow: hidden;
display: -webkit-box;
-webkit-box-orient: vertical;
-webkit-line-clamp: 3;
```

### css动画
box-shadow: 水平阴影的位置 垂直阴影的位置 模糊距离 阴影的大小 阴影的颜色 阴影开始方向（默认是从里往外，设置inset就是从外往里）;

animation: name duration timing-function delay iteration-count direction fill-mode play-state;  
translation: property duration timing-function delay   
@keyframe: from to;0% - 100%  
translate: 平移，rotate: 旋转，scale: 缩放

#### collapse

非table表现为hidden，table表现为none。chrome则两个都为none，firefox遵循原始规则

#### width:auto 和 width:100%的区别

width:100%会使元素box的宽度等于父元素的content-box的宽度。

width:auto会使元素撑满整个父元素，margin、border、padding、content区域会自动分配水平空间。

### 为什么img是inline还可以设置宽高

- 可替换元素（replaced element）的展现效果不是由 CSS 来控制的。这些元素是一种外部对象，它们外观的渲染，是独立于 CSS 的。  
- 可替换元素拥有内置宽高，他们可以设置width和height。他们的性质同设置了display:inline-block的元素一致。

### 响应式和自适应的区别，如何做

1. 响应式网页设计是自适应网页设计的子集，响应式布局等于流动网格布局，而自适应布局等于使用固定分割点来进行布局。
2. 一些响应式方法

```
@media screen and (max-width:100px);
<meta name="viewport" content="width=device-width, initial-scale=1.0,user-scalable=0">
```

### 伪类，伪元素

1. CSS伪类是用来添加一些选择器的特殊效果。
2. 伪元素是创造DOM之外的对象（清除浮动）

### 伪类nth-child和nth-of-type区别

举个例子 `<p><sapn></sapn><sapn></sapn></p>`

1. p:nth-child(2)指的是第一个span
2. p:nth-of-type(2)指的是第二个span

### white-space,word-break,word-wrap

- white-space，**控制空白字符的显示**，同时还能控制是否自动换行。它有五个值：`normal | nowrap | pre | pre-wrap | pre-line`

- word-break，**控制单词如何被拆分换行**。它有三个值：`normal | break-all | keep-all`

- word-wrap（overflow-wrap）**控制长度超过一行的单词是否被拆分换行**，是`word-break`的补充，它有两个值：`normal | break-word`

### position和float一起使用

1. 当position为absolute/fixed时，元素已脱离文档流，再对元素应用float失效（即不起作用）。
2. 当position为static/relative时，元素依旧处于普通流中，再对元素应用float起作用。

### 已知如下代码，如何修改才能让图片宽度为 300px ？注意下面代码不可修改。

```
<img src="1.jpg" style="width:480px!important;”>
```

1. css方法
   - max-width:300px;覆盖其样式；
   - transform: scale(0.625)；按比例缩放图片；
2. js方法
   document.getElementsByTagName("img")[0].setAttribute("style","width:300px!important;")

# 实践部分

## 布局

### 元素垂直水平居中

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>水平垂直居中</title>
    <style>
/* 公共代码 */
.wp {
    border: 1px solid red;
    width: 300px;
    height: 300px;
}

.box {
    background: green;    
}

.size{
    width: 100px;
    height: 100px;
}
/**方法1*/
/*
.wp {
    position: relative;
}
.box {
    position: absolute;;
    top: 50%;
    left: 50%;
    margin-left: -50px;
    margin-top: -50px;
}
/**方法2*/
/*
.wp {
    position: relative;
}
.box {
    position: absolute;;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    margin: auto;
}
/**方法3*/
/*
.wp {
    position: relative;
}
.box {
    position: absolute;;
    top: calc(50% - 50px);
    left: calc(50% - 50px);
}
/*方法四*/
/*
.wp {
    position: relative;
}
.box {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
}
/**方法5*/
/* 
.wp {
    line-height: 300px;
    text-align: center;
    font-size: 0px;
}
.box {
    font-size: 16px;
    display: inline-block;
    vertical-align: middle;
    line-height: initial;
    text-align: left; 
}
/**方法6*/
/*
.wp {
    display: table-cell;
    text-align: center;
    vertical-align: middle;
}
.box {
    display: inline-block;
}
/**方法7*/
/*
.wp {
    display: flex;
    justify-content: center;
    align-items: center;
}
/**方法8*/
.wp{
    display:grid;
}
.box{
    justify-self: center;
    align-self: center;
}

    </style>
</head>
<body>
    <div class="wp">
        <div class="box">123123</div>
    </div>
    
</body>
</html>
```
### 三栏布局

1. absolute + margin
2. float + overflow
3. flex + order(content box 的width为100%)
4. 圣杯布局: 父容器设置左浮动并预留左右子容器的padding box，左容器设置margin-left=-100%,left=-width;右容器设置margin-left=-width,right=-width;中间容器自适应
```css
        .root{
            height: 100%;
            margin: 0;
            padding: 0 200px;
        }
        .left,.right,.mid{
            position: relative;
            float: left;
            height: 100%;
        }
        .left{
            width: 200px;
            background: red;
            margin-left: -100%;
            left: -200px;
        }
        .right{
            width: 200px;
            background: skyblue;
            margin-left: -200px;
            right: -200px;
        }
        .mid{
            width: 100%;
            background: green;
        }
```
5. 双飞翼布局: 类似于双飞翼，但使用了子元素margin代替父容器的padding
```css
        .root{
            height: 100%;
            margin: 0;
        }
        .left,.right,.mid{
            position: relative;
            float: left;
            height: 100%;
        }
        .left{
            width: 200px;
            background: red;
            margin-left: -100%;
        }
        .right{
            width: 200px;
            background: skyblue;
            margin-left: -200px;
        }
        .mid{
            width: 100%;
            background: green;
        }
        .child{
           margin: 0 200px;
        }
```

### 两列等高布局

```css
//1
.container{
  display:flex;
}
.left,.right{
  flex:1;
}
.left{
  background:pink;
}
.right{
  background:green;
}

//2

// 在这里有两列，故需要两个容器
#container2 {
  float: left;
  width: 100%;
  background: orange;
  position: relative;
  overflow: hidden;
}

#container1 {
  float: left;
  width: 100%;
  background: green;
  position: relative;
  right: 30%;/* 距离是第二列的宽度，加上2%的padding */
}

#col1 {
  width: 66%;
  float: left;
  position: relative;
  left: 32%;/* container1容器right:30%;加上内边距2%，故为32%；  */
}

#col2 {
  width: 26%;
  float: left;
  position: relative;
  left: 36%;/* 加上三个内边距，所以是 36%；*/
}

//3
#container {
  margin: 0 auto;
  overflow: hidden;
  width: 960px;
}

.column {
  background: #ccc;
  float: left;
  width: 200px;
  margin-right: 5px;
  margin-bottom: -99999px;
  padding-bottom: 99999px;
}

#content {
  background: #eee;
}

#right {
  float: right;
  margin-right: 0;
}

//4
#containerOuter {
  margin: 0 auto;
  width: 960px;
}

#container {
  background-color: #0ff;
  float: left;
  width: 520px;
  border-left: 220px solid #0f0;
  /* 边框大小等于左边栏宽度，颜色和左边栏背景色一致*/
  border-right: 220px solid #f00;
  /* 边框大小等于右边栏宽度，颜色和右边栏背景色一致*/
}

#left {
  float: left;
  width: 200px;
  margin-left: -220px;
  padding:10px;
  position: relative;
/*   测试 */
  border:1px solid;
}

#content {
  float: left;
  width: 500px;
  padding:10px;  
  margin-right: -520px;
}

#right {
  float: right;
  width: 200px;  
  padding:10px;
  margin-right: -220px;
  position: relative;
}
```

## 奇怪的知识

### 画正方体，三角形，进度条

```css
//三角形
#test{
    width: 0;
    height: 0;
    border-top: 100px solid black;
    border-left: 100px solid transparent;
    border-right: 100px solid transparent;
    border-bottom: 100px solid transparent;
        
}
//正方体
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>perspective</title>
<style>
.wrapper{
width: 50%;
float: left;
}
.cube{
font-size: 4em;
width: 2em;
margin: 1.5em auto;
transform-style:preserve-3d;
transform:rotateX(-30deg) rotateY(30deg);
}
.side{
position: absolute;
width: 2em;
height: 2em;
background: rgba(255,99,71,0.6);
border: 1px solid rgba(0,0,0,0.5);
color: white;
text-align: center;
line-height: 2em;
}
.front{
transform:translateZ(1em);
}
.bottom{
transform:rotateX(-90deg) translateZ(1em);
}
.top{
transform:rotateX(90deg) translateZ(1em);
}
.left{
transform:rotateY(-90deg) translateZ(1em);
}
.right{
transform:rotateY(90deg) translateZ(1em);
}
.back{
transform:translateZ(-1em);
}
</style>
</head>
<body>
<div class="wrapper w1">
    <div class="cube">
        <div class="side front">1</div>
        <div class="side back">6</div>
        <div class="side right">4</div>
        <div class="side left">3</div>
        <div class="side top">5</div>
        <div class="side bottom">2</div>
    </div>
</div>

</body>
</html>
//滚动条
        div{
            height: 10px;
            border: 1px solid;
            background: linear-gradient(#0ff,#0ff);
            background-repeat: no-repeat;
            background-size: 0;
            animation: move 2s linear forwards;
        }
        @keyframes move {
            100%{
                background-size: 100%;
            }
        }
```

### 每天一个奇怪的知识点

[CSS Tricks](https://juejin.im/post/5aab4f985188255582521c57)

### css滚动视差

多层背景以不同的速度移动，形成立体的运动效果

background-attachment

```css
<section class="g-word">Header</section>
<section class="g-img">IMG1</section>
<section class="g-word">Content1</section>
<section class="g-img">IMG2</section>
<section class="g-word">Content2</section>
<section class="g-img">IMG3</section>
<section class="g-word">Footer</section>

section {
    height: 100vh;
}

.g-img {
    background-image: url(...);
    background-attachment: fixed;
    background-size: cover;
    background-position: center center;
}

```

transform: translate3d

```css
<div class="g-container">
    <div class="section-one">translateZ(-1)</div>
    <div class="section-two">translateZ(-2)</div>
    <div class="section-three">translateZ(-3)</div>
</div>

html {
    height: 100%;
    overflow: hidden;
}

body {
    perspective: 1px;
    transform-style: preserve-3d;
    height: 100%;
    overflow-y: scroll;
    overflow-x: hidden;
}

.g-container {
    height: 150%;

    .section-one {
        transform: translateZ(-1px);
    }
    .section-two {
        transform: translateZ(-2px);
    }
    .section-three {
        transform: translateZ(-3px);
    }
}
```

## 常用需求

### 请用CSS写一个简单的幻灯片效果页面

```
/**css**/
.ani{
  width:480px;
  height:320px;
  margin:50px auto;
  overflow: hidden;
  box-shadow:0 0 5px rgba(0,0,0,1);
  background-size: cover;
  background-position: center;
  -webkit-animation-name: "loops";
  -webkit-animation-duration: 20s;
  -webkit-animation-iteration-count: infinite;
}
@-webkit-keyframes "loops" {
    0% {
        background:url(http://d.hiphotos.baidu.com/image/w%3D400/sign=c01e6adca964034f0fcdc3069fc27980/e824b899a9014c08e5e38ca4087b02087af4f4d3.jpg) no-repeat;             
    }
    25% {
        background:url(http://b.hiphotos.baidu.com/image/w%3D400/sign=edee1572e9f81a4c2632edc9e72b6029/30adcbef76094b364d72bceba1cc7cd98c109dd0.jpg) no-repeat;
    }
    50% {
        background:url(http://b.hiphotos.baidu.com/image/w%3D400/sign=937dace2552c11dfded1be2353266255/d8f9d72a6059252d258e7605369b033b5bb5b912.jpg) no-repeat;
    }
    75% {
        background:url(http://g.hiphotos.baidu.com/image/w%3D400/sign=7d37500b8544ebf86d71653fe9f9d736/0df431adcbef76095d61f0972cdda3cc7cd99e4b.jpg) no-repeat;
    }
    100% {
        background:url(http://c.hiphotos.baidu.com/image/w%3D400/sign=cfb239ceb0fb43161a1f7b7a10a54642/3b87e950352ac65ce2e73f76f9f2b21192138ad1.jpg) no-repeat;
    }
}
```

### css九宫格

```html
//float
<style>
    .float{
      margin: 50px; //为了和页面中的其他块拉开距离
      height: 300px;
      width: 300px;
    }
    .float > li{
      box-sizing: border-box;
      float:left;
      width: 100px;
      height: 100px;
      margin-left: -4px;
      margin-top: -4px;
      line-height: 100px;
      text-align: center;
      list-style: none;
      border:4px solid #ccc;
    }
    .float > li:hover{
      border-color: red;
      position: relative;
    }
  </style>
  <ul class="float">
    <li>1</li>
    <li>2</li>
    <li>3</li>
    <li>4</li>
    <li>5</li>
    <li>6</li>
    <li>7</li>
    <li>8</li>
    <li>9</li>
  </ul>

//flex
<style>
.flex{
      display: flex;
      width: 300px;
      /*height: 300px;*/
      margin: 50px;
      flex-wrap: wrap;
      /*align-content: flex-start;      */
      box-sizing: border-box;           
    }
    .flex > li{
      box-sizing: border-box;
      height: 100px;
      width: 100px;
      margin-left: -4px;
      margin-top: -4px;
      line-height: 100px;
      text-align: center;
      list-style: none;
      border: 4px solid #ccc;
    }

    .flex > li:hover{
      border-color:red;
      position: relative;
      /*z-index:2;*/
    }
</style>
 <ul class="flex">
    <li>1</li>
    <li>2</li>
    <li>3</li>
    <li>4</li>
    <li>flex</li>
    <li>6</li>
    <li>7</li>
    <li>8</li>
    <li>9</li>
  </ul>

//table
<style>
    .table{
      margin-top: 100px;
      width: 300px;
      height: 300px;
      text-align: center;
      border: 4px solid #ccc;
      border-collapse: collapse;
      box-sizing: border-box;
    }
    .table td{
      /*height: 100px;*/
      width: 100px;
      vertical-align: middle;
      border: 4px solid #ccc;
      text-align: center;
      box-sizing: border-box;
      line-height: 100px;
    }
    .table td:hover{
      border-color: red;      
      position: absolute;
      width: 94px;
      height: 100px;
      margin-top: -4px;
      margin-left: -4px;
      box-sizing: content-box;
    }
  </style>
  <table class="table">
    <tr>
      <td>1</td>
      <td>2</td>
      <td>3</td>
    </tr>
    <tr>
      <td>1</td>
      <td>table</td>
      <td>3</td>
    </tr>
    <tr>
      <td>1</td>
      <td>2</td>
      <td>3</td>
    </tr>
  </table>

```

### CSS实现滚动指示器

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CSS滚动指示器</title>
    <style>
    body {
        position: relative;
        height: 2000px;
    }
    .indicator {
        position: absolute;
        top: 0; right: 0; left: 0; bottom: 0;
        background: linear-gradient(to right top, teal 50%, transparent 50%) no-repeat;
        background-size: 100% calc(100% - 100vh);   /*这里减去100vh是为了滑动到最底部时能够刚好与右上角贴合*/
        z-index: 1;
        pointer-events: none;
        mix-blend-mode: darken; /*两个颜色进行混合，哪个颜色深就用哪个，防止白色蒙板覆盖容器内部的内容*/
    }
    .indicator::after {
        content: '';
        position: fixed;
        top: 5px; bottom: 0; right: 0; left: 0;
        background: #fff;
        z-index: 1;
    }
    </style>
</head>
<body>
    <div class="indicator"></div>
</body>
</html>
```

### 面包屑

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>breadcrumb</title>
</head>
<style>
    ul.breadcrumb {
    padding: 8px 16px;
    list-style: none;
    background-color: #eee;
    }
    ul.breadcrumb li {
        display: inline;
    }
    ul.breadcrumb li::after {
        padding: 8px;
        color: black;
        content: "/\00A0";  /*\00A0表示空格*/
    }
    ul.breadcrumb li a {
        color: green;
    }

</style>
<body>
    <ul class="breadcrumb">
        <li><a href="#">Home</a>
        </li>
        <li><a href="#">Pictures</a>
        </li>
        <li><a href="#">Summer 15</a>
        </li>
        <li>Italy</li>
    </ul>
    
</body>
</html>
```

### 自动打字

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title></title>
    </head>
    <style>
        .tagline,
        .tagline-skill {
            height: 80px;
        }
        .tagline {
            font-family: "Fira Mono", monospace;
            overflow: hidden;
            background-color: #2a2a28;
            color: #fff;
        }
        .tagline-skill {
            position: relative;
            font-size: 1.5em;
            padding-top: .75em;
            -webkit-animation: animatetotop 6s steps(3) infinite;
            animation: animatetotop 6s steps(3) infinite;
        }
        .tagline-skill_inner,
        .tagline-skill-overlay {
            display: inline-block;
        }
        .tagline-skill_inner {
            position: relative;
            line-height: 1;
        }
        .tagline-skill_inner:after {
            content: "";
            position: absolute;
            top: -1px;
            right: 0;
            bottom: -2px;
            left: 0;
            border-left: 1px solid #fff;
            background-color: #2a2829;
            -webkit-animation: animatetoright 1s steps(10) infinite alternate;
            animation: animatetoright 1s steps(10) infinite alternate;
        }
        @-webkit-keyframes animatetoright {
            0% {
                left: 0;
                margin-right: auto;
            }
            100% {
                left: 100%;
                margin-left: .6em;
                margin-right: -.6em;
            }
        }
        @keyframes animatetoright {
            0% {
                left: 0;
                margin-right: auto;
            }
            100% {
                left: 100%;
                margin-left: .6em;
                margin-right: -.6em;
            }
        }
        @-webkit-keyframes animatetotop {
            0% {
                top: 0;
            }
            100% {
                top: -240px;
            }
        }
        @keyframes animatetotop {
            0% {
                top: 0;
            }
            100% {
                top: -240px;
            }
        }
        /* general layout */
        
        * {
            margin: 0;
            padding: 0;
            box-sizing: inherit;
        }
        html {
            font-size: 16px;
        }
        html,
        body {
            height: 100%;
            background: background;
        }
        body {
            font: 100%/1.5 sans-serif;
            min-height: 100%;
            box-sizing: border-box;
        }
        header {
            text-align: center;
            padding: 1em;
        }
        header h1,
        .tagline,
        .tagline-skill {
            width: 192px;
            display: block;
            font-weight: 400;
        }
        header h1 {
            font-family: "Fira Sans", sans-serif;
            font-size: 1.75em;
            height: 112px;
            padding: .5em;
            background-color: #f2de03;
        } 
        </style>
	<body>
		<header>
		    <h1>Typing Animation</h1>
		    <p class="tagline">
		        <span class="tagline-skill"><span class="tagline-skill_inner">webdesign</span></span>
		    </p>
		</header>
	</body>
</html>
```

### 有用的代码片段

[code snippet](https://juejin.im/post/5b1f41246fb9a01e725131fb)