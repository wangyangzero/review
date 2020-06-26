## Webpack构建

#### 构建流程

配置文件中读取与合并参数 => 使用参数初始化complier对象，加载plugin执行run方法 => 通过entry寻找入口文件 => 从入口文件开始使用loader翻译模块 => 根据入口文件与模块之间的依赖关系打包为一个或多个chunk => 根据配置的filename，path将打包后的文件写入到文件系统。

1. entry 
   Entry是Webpack的入口起点指示，它指示webpack应该从哪个模块开始着手，来作为其构建内部依赖图的开始。

```js
//webpack.config.js
module.exports = {
    entry: {
        app: './app.js',
        vendors: './vendors.js'
    }
};
```

2. output
   webpack打包后输出配置

```
output: {
    filename: 'bundle.js',
    path: '/home/proj/public/dist'
}

```

3. loader
   loader可以理解为webpack的编译器，它使得webpack可以处理一些非JavaScript文件

```js
{
    module: {
        rules: [
            {
                test: /\.css$/,
                use: [
                    {
                        loader: 'style-loader'
                    },
                    {
                        loader: 'css-loader'
                    }
                ]
            }
        ]
    }
}
```

4. plugin
   插件是webpack的支柱功能，目前主要是解决loader无法实现的其他许多复杂功能

```javascript
// webpack.config.js
const webpack = require('webpack');
module.exports = {
    plugins: [
        new webpack.optimize.UglifyJsPlugin()
    ]
}
```

5. mode
   指定以开发者模式还是产品模式打包development || production

#### loader和plugin的区别

1. loader是一个转换器，作用于文件。
2. plugin是一个扩展器，它并不直接操作文件，而是基于事件监听机制工作，会监听webpack打包过程中的一些节点，执行广泛的任务.

#### webpack优化

1. noParse跟一个正则表达式，用来匹配无需解析的模块
2. mode设置为production模式
3. 给babel-loader设置缓存，cacheDirectory特定选项（默认 false）

```js
module.exports = {
  module: {
    rules: [
      {
        // js 文件才使用 babel
        test: /\.js$/,
        loader: 'babel-loader',
        // 只在 src 文件夹下查找
        include: [resolve('src')],
        // 不会去查找的路径
        exclude: /node_modules/
      }
    ],
   optimization: {
       splitChunks: {
           cacheGroup: {
               chunkname:{
                   chunks: 'initial',
                   name: '打包后的名字',
                   minSize: 0
               }
           }
       }
   }
  }
}
loader: 'babel-loader?cacheDirectory=true'
```

#### webpack热更新

1. 当修改了一个或多个文件；
2. 文件系统接收更改并通知webpack；
3. webpack重新编译构建一个或多个模块，并通知HMR服务器进行更新；
4. HMR Server 使用webSocket通知HMR runtime 需要更新，HMR runtime通过HTTP请求更新；
5. HMR runtime替换更新中的模块，如果确定这些模块无法更新，则触发整个页面刷新。

## webpack性能优化

### 构建速度优化

`Webpack`进行打包时会根据`Entry`配置的入口出发、递归地解析所依赖的文件。整个过程分为**查询文件**和对**匹配到的文件进行分析、转化**这两个过程。

#### 缩小文件的搜索范围

**`resolve`**字段会告诉`Webpack`怎么去查询文件

```js
// 配置查询目录
// webpack会先查询./node_modules 然后是../node_modules....
resolve.modules:[path.resolve(__dirname,'node_modules')]

// 配置入口文件
// 第三方模块往往有多个入口文件，可以设置单独一个main值，减少搜索
resolve.mainFields:['main']

// 第三方模块依赖尽量使用压缩包
resolve.alias:{
	'react':patch.resolve(__dirname, './node_modules/react/dist/react.min.js')
}

// 配置文件查找顺序
// 列表值尽可能少；频率高的文件类型后缀写在前面
// 源码中的导入语句尽可能的写上文件后缀
resolve.extensions: ['.js','.json']
```

#### 减少基础模块编译次数

`DllPlugin`全称动态链接插件，其原理是将网页依赖的基础模块抽离出来打包到`dll`文件

```js
// webpack_dll.config.js
const path = require('path');
const DllPlugin = require('webpack/lib/DllPlugin');
module.exports = {
 entry:{
     react:['react','react-dom'],
     polyfill:['core-js/fn/promise','whatwg-fetch']
 },
 output:{
     filename:'[name].dll.js',
     path:path.resolve(__dirname, 'dist'),
     library:'_dll_[name]',  //dll的全局变量名
 },
 plugins:[
     new DllPlugin({
         name:'_dll_[name]',  //dll的全局变量名
         path:path.join(__dirname,'dist','[name].manifest.json'),//描述生成的manifest文件,打包到dist文件夹下
     })
 ]
}

// 文件结构
 |-- polyfill.dll.js
 |-- polyfill.manifest.json
 |-- react.dll.js
 └── react.manifest.json

// 也可以引入配置好的manifest.json文件
//webpack.config.json
const path = require('path');
const DllReferencePlugin = require('webpack/lib/DllReferencePlugin');
module.exports = {
    entry:{ main:'./main.js' },
    //... 省略output、loader等的配置
    plugins:[
        new DllReferencePlugin({
            manifest:require('./dist/react.manifest.json')
        }),
        new DllReferenctPlugin({
            manifest:require('./dist/polyfill.manifest.json')
        })
    ]
}
```

#### 使用`HappyPack`开启多进程`Loader`转换

整个构建过程中，最费时的就是`Loader`对文件进行转译的操作了。

`HappyPack`可以将任务分解给多个子进程，最后将结果发送给主进程。

```js
npm i -D happypack
// webpack.config.json
const path = require('path');
const HappyPack = require('happypack');

module.exports = {
    //...
    module:{
        rules:[{
                test:/\.js$/，
                use:['happypack/loader?id=babel']
                exclude:path.resolve(__dirname, 'node_modules')
            },{
                test:/\.css/,
                use:['happypack/loader?id=css']
            }],
        plugins:[
            new HappyPack({
                id:'babel',
                loaders:['babel-loader?cacheDirectory']
            }),
            new HappyPack({
                id:'css',
                loaders:['css-loader']
            })
        ]
    }
}
```

#### `ParallelUglifyPlugin`开启多进程压缩JS文件

`ParallelUglifyPlugin`开启多进程，每个子进程使用`UglifyJS`插件压缩代码

```js
npm i -D webpack-parallel-uglify-plugin

// webpack.config.json
const ParallelUglifyPlugin = require('wbepack-parallel-uglify-plugin');
//...
plugins: [
    new ParallelUglifyPlugin({
        uglifyJS:{
            //...这里放uglifyJS的参数
        },
        //...其他ParallelUglifyPlugin的参数，设置cacheDir可以开启缓存，加快构建速度
    })
]
```

### 优化输出质量--压缩代码体积

#### 使用产品模式

```js
const DefinePlugin = require('webpack/lib/DefinePlugin');
//...
plugins:[
    new DefinePlugin({
        'process.env': {
            NODE_ENV: JSON.stringify('production')	// 配置构建环境的环境变量
        }
    })
]

```

#### 使用插件压缩代码

```js
// 压缩JS
const UglifyJSPlugin = require('webpack/lib/optimize/UglifyJsPlugin');
//...
plugins: [
    new UglifyJSPlugin({
        compress: {
            warnings: false,  //删除无用代码时不输出警告
            drop_console: true,  //删除所有console语句，可以兼容IE
            collapse_vars: true,  //内嵌已定义但只使用一次的变量
            reduce_vars: true,  //提取使用多次但没定义的静态值到变量
        },
        output: {
            beautify: false, //最紧凑的输出，不保留空格和制表符
            comments: false, //删除所有注释
        }
    })
]

// 压缩ES
npm i -D uglify-webpack-plugin@beta //要使用最新版本的插件
//webpack.config.json
const UglifyESPlugin = require('uglify-webpack-plugin');
//...
plugins:[
    new UglifyESPlugin({
        uglifyOptions: {  //比UglifyJS多嵌套一层
            compress: {
                warnings: false,
                drop_console: true,
                collapse_vars: true,
                reduce_vars: true
            },
            output: {
                beautify: false,
                comments: false
            }
        }
    })
]

// 压缩CSS
// 配置cssloader时
css-loader?minimize、PurifyCSSPlugin
```

#### 使用`Tree Shaking`剔除无用代码

##### 本质

tree-sharking的本质是通过静态检查，判断出冗杂代码（不影响输出），然后消除这些代码。检查的依据是通过分析程序流，判断哪些变量未被使用、引用进而删除代码。

##### 为什么与ES6密切绑定

**ES6 module的特点**

- 只能作为模块顶层的语句出现
- 模块之间的依赖关系是可以确定的，与运行时的状态无关
- import的模块名只能是字符串常量

##### webpack旧版本为什么会出现tree-sharking看似失效的情况

原因是函数式编程带来的副作用，一种简单的情况是未被使用的函数引用了外部的变量。

而且使用webpack打包时通常会使用babel进行重新编码以适应各种版本，在babel转码时也会带来一些副作用

```js
export class Person {
  constructor ({ name, age, sex }) {
    this.className = 'Person'
    this.name = name
    this.age = age
    this.sex = sex
  }
  getName () {
    return this.name
  }
}
export class Apple {
  constructor ({ model }) {
    this.className = 'Apple'
    this.model = model
  }
  getModel () {
    return this.model
  }
}
// main.js
import { Apple } from './components'

const appleModel = new Apple({
  model: 'IphoneX'
}).getModel()

console.log(appleModel)

```

##### 在webpack中配置tree-sharking

1. 修改.babelrc以保留ES6模块化语句：

   ```js
   {
       "presets": [
           [
               "env", 
               { "module": false },   //关闭Babel的模块转换功能，保留ES6模块化语法
           ]
       ]
   }
   ```
   
2. 启动webpack时带上 --display-used-exports可以在shell打印出关于代码剔除的提示

3. 使用UglifyJSPlugin，或者启动时使用--optimize-minimize

4. 在使用第三方库时，需要配置 `resolve.mainFields: ['jsnext:main', 'main']` 以指明解析第三方库代码时，采用ES6模块化的代码入口

[tree-sharking配置](https://webpack.js.org/guides/tree-shaking/#root)

### 优化输出质量--加速网络请求

#### 使用`CDN`加载静态资源

**业界做法**

- **HTML**文件：放在服务器上且关闭缓存，不接入`CDN`
- **JS，CSS，图片等资源**：开启`CDN`和缓存，同时名字上带上`Hash`值，这样`hash`变化，文件就会变化，就会重新下载且不管缓存时间的长短

**构建要点**

- 静态资源导入的`URL`要变成指向`CDN`服务的绝对路劲的`URL`
- 静态资源的文件名要带上根据内容计算出的`Hash`值
- 不同类型资源放在不同域名的`CDN`上

**配置**

```js
const ExtractTextPlugin = require('extract-text-webpack-plugin');
const {WebPlugin} = require('web-webpack-plugin');
//...
output:{
 filename: '[name]_[chunkhash:8].js',
 path: path.resolve(__dirname, 'dist'),
 publicPatch: '//js.cdn.com/id/', //指定存放JS文件的CDN地址
},
module:{
 rules:[{
     test: /\.css/,
     use: ExtractTextPlugin.extract({
         use: ['css-loader?minimize'],
         publicPatch: '//img.cdn.com/id/', //指定css文件中导入的图片等资源存放的cdn地址
     }),
 },{
    test: /\.png/,
    use: ['file-loader?name=[name]_[hash:8].[ext]'], //为输出的PNG文件名加上Hash值 
 }]
},
plugins:[
  new WebPlugin({
     template: './template.html',
     filename: 'index.html',
     stylePublicPath: '//css.cdn.com/id/', //指定存放CSS文件的CDN地址
  }),
 new ExtractTextPlugin({
     filename:`[name]_[contenthash:8].css`, //为输出的CSS文件加上Hash
 })
]

```

#### 多页面提取页面间的公共代码，以利用缓存

**抽离公共代码的方法**

1. 将多个页面依赖的公共代码提取到`common.js`中，（此时`common.js`中包含基础库的代码）

   ```js
   const CommonsChunkPlugin = require('webpack/lib/optimize/CommonsChunkPlugin');
   //...
   plugins:[
       new CommonsChunkPlugin({
           chunks:['a','b'], //从哪些chunk中提取
           name:'common',  // 提取出的公共部分形成一个新的chunk
       })
   ]
   ```

2. 为了找出公共代码中的基础库，写一个`base.js`文件，再从`common.js`提取公共代码到`base`中，`common.js`就剔除了基础库的代码，而`base.js`保持不变

   ```js
   //base.js
   import 'react';
   import 'react-dom';
   import './base.css';
   //webpack.config.json
   entry:{
       base: './base.js'
   },
   plugins:[
       new CommonsChunkPlugin({
           chunks:['base','common'],
           name:'base',
           //minChunks:2, 表示文件要被提取出来需要在指定的chunks中出现的最小次数，防止common.js中没有代码的情况
       })        
   ]
   ```
   
3. 至此得到了基础库代码`base.js`，不含基础库的公共代码`common.js`，和页面各自的代码文件`page.js`

#### 分割代码实现按需加载

**分割原理**

1. 将网站功能按照相关程度划分为几类
2. 每一类合并成一个`Chunk`，按需加载对应的`Chunk`，比如首屏加载时只需要把首屏相关的功能放在执行入口所在的`Chunk`

**使用方法**

`import(/* webpackChunkName:show */ './show').then()` 是实现按需加载的关键，Webpack内置对import( *)语句的支持，Webpack会以`./show.js`为入口重新生成一个Chunk。代码在浏览器上运行时只有点击了按钮才会开始加载show.js，且import语句会返回一个Promise，加载成功后可以在then方法中获取加载的内容。这要求浏览器支持Promise API，对于不支持的浏览器，需要注入Promise polyfill。`/* webpackChunkName:show */` 是定义动态生成的Chunk的名称，默认名称是[id].js，定义名称方便调试代码。为了正确输出这个配置的ChunkName，还需要配置Webpack：

```js
//main.js
document.getElementById('btn').addEventListener('click',function(){
    import(/* webpackChunkName:"show" */ './show').then((show)=>{
        show('Webpack');
    })
})
//show.js
module.exports = function (content) {
    window.alert('Hello ' + content);
}

// 配置
output:{
    filename:'[name].js',
    chunkFilename:'[name].js', //指定动态生成的Chunk在输出时的文件名称
}

```

### 优化输出质量--提高代码运行时的效率

#### Prepack提前求值

**原理**

`Prepack`在编译时将计算结果放在编译后的代码里，而不是运行时计算

**使用**

```js
const PrepackWebpackPlugin = require('prepack-webpack-plugin').default;
module.exports = {
    plugins:[
        new PrepackWebpackPlugin()
    ]
}
```

#### 使用`Scope Hoisting`

**原理**

依赖于`ES6`，它分析模块间的依赖关系，尽可能将被打散的模块合并到一个函数中，但不能造成代码冗余，所以只有被引用一次的模块才能被合并。（感觉有点鸡肋昂，使用于单页面合并插件包）

**使用方法**

```js
const ModuleConcatenationPlugin = require('webpack/lib/optimize/ModuleConcatenationPlugin');
//...
plugins:[
    new ModuleConcatenationPlugin();
],
resolve:{
	mainFields:['jsnext:main','browser','main']
}

```

### 一些分析工具

#### 官方

使用`Webpack`打包时加上这些参数`Webpack --profile --json > stats.json`

其中 `--profile` 记录构建过程中的耗时信息，`--json` 以JSON的格式输出构建结果，`>stats.json` 是UNIX / Linux系统中的管道命令，含义是将内容通过管道输出到stats.json文件中。

**官方工具Webpack Analyse**

打开该工具的官网http://webpack.github.io/analyse/上传stats.json，就可以得到分析结果

**webpack-bundle-analyzer**

可视化分析工具，比Webapck Analyse更直观。使用也很简单：

1. npm  i -g webpack-bundle-analyzer安装到全局
2. 按照上面方法生成stats.json文件
3. 在项目根目录执行`webpack-bundle-analyzer` ，浏览器会自动打开结果分析页面

### 其他知识点

- 配置`babel`时，`use: ['babel-loader?cacheDirectory']`增加缓存配置，加快重新编译的速度
- 配置`noParse: true`排除没使用的模块化语句
- 配置`performance`参数可以输出文件的性能检查配置
- 配置`profile: true`，用于分析是什么原因导致构建性能不佳
- 配置`cache: true`，启用缓存提升构建速度
- 开发环境下将`devtool`设置为`cheap-module-eval-source-map`，因为生成这种`source map`的速度最快，能加速构建。在生产环境下将`devtool`设置为`hidden-source-map`



