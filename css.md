### 盒模型
#### w3c盒模型
width标识内容宽度，块宽度 = width + paddding + border + margin
#### ie盒模型
width标识内容宽度 + padding + border，块宽度 = width + margin
#### box-sizing
1. content-box: w3c盒子
2. border-box: ie盒子
3. padding-box: padding为界的盒子
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

### link和import的区别

link可以加载css以外的文件如预加载js文件，import只能加载css

link在页面载入时加载，import在页面载入完成后加载

link无兼容问题

link支持js控制dom改变样式

```html
<link as=script href='' rel=preload>
```

### 消除图片底部间隙的方法

```css
图片块状化 - 无基线对齐：img { display: block; }
图片底线对齐：img { vertical-align: bottom; }
行高足够小 - 基线位置上移：.box { line-height: 0; }
```

### css颜色体系

![image](https://user-gold-cdn.xitu.io/2018/6/7/163dabeee8cdb4e5?imageslim)

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

### position
固定定位fixed： 

元素的位置相对于浏览器窗口是固定位置，即使窗口是滚动的它也不会移动。Fixed定位使元素的位置与文档流无关，因此不占据空间。 Fixed定位的元素和其他元素重叠。  

相对定位relative：  

如果对一个元素进行相对定位，它将出现在它所在的位置上。然后，可以通过设置垂直或水平位置，让这个元素“相对于”它的起点进行移动。 在使用相对定位时，无论是否进行移动，元素仍然占据原来的空间。因此，移动元素会导致它覆盖其它框。  

绝对定位absolute：  

绝对定位的元素的位置相对于最近的已定位父元素，如果元素没有已定位的父元素，那么它的位置相对于`<html>`。 absolute 定位使元素的位置与文档流无关，因此不占据空间。 absolute 定位的元素和其他元素重叠。  

粘性定位sticky：  

元素先按照普通文档流定位，然后相对于该元素在流中的flow root（BFC）和 containing block（最近的块级祖先元素）定位。而后，元素定位表现为在跨越特定阈值前为相对定位，之后为固定定位。  

默认定位Static：  

默认值。没有定位，元素出现在正常的流中（忽略top, bottom, left, right 或者 z-index 声明）。  

inherit:  

规定应该从父元素继承position 属性的值。

### css动画
box-shadow: 水平阴影的位置 垂直阴影的位置 模糊距离 阴影的大小 阴影的颜色 阴影开始方向（默认是从里往外，设置inset就是从外往里）;

animation: name duration timing-function delay iteration-count direction fill-mode play-state;  
translation: property duration timing-function delay   
@keyframe: from to;0% - 100%  
translate: 平移，rotate: 旋转，scale: 缩放

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
#### css如何阻塞文档解析

```
如果浏览器尚未完成 CSSOM 的下载和构建，而我们却想在此时运行脚本，那么浏览器将延迟 JavaScript 脚本执行和文档的解析，直至其完成 CSSOM 的下载和构建。
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

#### ifc
当块级元素内部只有内联元素时生效
1. 只计算横向空间不计算纵向
2. 设置text-align:center可以实现多元素水平居中
3. float元素优先排列

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

### 为什么img是inline还可以设置宽高
- 可替换元素（replaced element）的展现效果不是由 CSS 来控制的。这些元素是一种外部对象，它们外观的渲染，是独立于 CSS 的。  
- 可替换元素拥有内置宽高，他们可以设置width和height。他们的性质同设置了display:inline-block的元素一致。
### 清除浮动的方法，overflow的原理
1. 设置一个带clear属性的空元素
2. 父元素设置浮动
3. 设置父元素的zoom为1,浮动元素设置overflow为hidden
4. display：inline-block也可以清除浮动
5. after伪元素

- overflow原理：构成bfc，内部只有一个浮动元素而浮动元素也会参与bfc容器高度计算，因此浮动就消失了
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

### 使用过flex布局吗？flex-grow和flex-shrink属性有什么用？
1. flex-grow，当父控件还有剩余空间的时候，是否进行放大
2. flex-shrink，当父控件空间不够的时候，是否进行缩小
3. flex-basis，项目的长度auto，px，em，%
4. auto 11 auto none 00 auto flex:1 1 1 0% default: 0 1 auto

### 响应式和自适应的区别，如何做
[more](http://www.alloyteam.com/2015/04/zi-shi-ying-she-ji-yu-xiang-ying-shi-wang-ye-she-ji-qian-tan/)
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
[more](https://juejin.im/post/5d1451176fb9a07f0052ebcf)
举个例子 `<p><sapn></sapn><sapn></sapn></p>`

1. p:nth-child(2)指的是第一个span
2. p:nth-of-type(2)指的是第二个span

### input属性相关，以及怎样做的文件上传
1. 创建input，设置file属性
2. 创建button，创建onclick方法
3. 通过dom操作获取input.files[0]，然后创建FormData();
4. axios上传

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

### base64的原理和优缺点
1. 优点可以加密，减少了HTTTP请求
2. 缺点是需要消耗CPU进行编解码
3. 用于减少 HTTP 请求
4. 适用于小图片
5. base64的体积约为原图的4/3

```js
function getImageBase64(img, ext) {
var canvas = document.createElement("canvas"); //创建canvas DOM元素，并设置其宽高和图片一样
canvas.width = img.width;
canvas.height = img.height;
var ctx = canvas.getContext("2d");
ctx.drawImage(img, 0, 0, img.width, img.height); //使用画布画图
var dataURL = canvas.toDataURL("image/" + ext); //返回的是一串Base64编码的URL并指定格式
canvas = null; //释放
return dataURL;
}
```



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

#### collapse

非table表现为hidden，table表现为none。chrome则两个都为none，firefox遵循原始规则

#### width:auto 和 width:100%的区别

width:100%会使元素box的宽度等于父元素的content-box的宽度。

width:auto会使元素撑满整个父元素，margin、border、padding、content区域会自动分配水平空间。