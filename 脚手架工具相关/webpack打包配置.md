# webpack打包教程

### 什么是webpack

> 本质上，webpack 是一个现代 JavaScript 应用程序的静态模块打包器(module bundler)。当 webpack 处理应用程序时，它会递归地构建一个依赖关系图(dependency graph)，其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个 bundle。

### 包含内容

#### 1、入口（entry）

> 入口起点(entry point)指示 webpack 应该使用哪个模块，来作为构建其内部依赖图的开始。进入入口起点后，webpack 会找出有哪些模块和库是入口起点（直接和间接）依赖的。每个依赖项随即被处理，最后输出到称之为 bundles 的文件中。
通常情况下是目录下的名为：webpack.config.js 的文件：

> ``` 
> module.exports = {    
>   entry: './path/to/my/entry/file.js'      
> }; 
> ```        

#### 2、输出(output)

配置打包后数据的输出文件，一般叫做：bundles(打包成一捆一捆的文件)，下面就是配置输出的文件叫做：my-first-webpack.bundle.js

> ``` 
> const path = require('path');    
> module.exports = {     
>   entry: './path/to/my/entry/file.js',        
>   output: {     
>     path: path.resolve(__dirname, 'dist'),     
>     filename: 'my-first-webpack.bundle.js'    
>   }         
> }; 
> ```    

#### 3、加载器(loader)

在 webpack 的配置中 loader 有两个目标：

a、test 属性，用于标识出应该被对应的 loader 进行转换的某个或某些文件。比如说：引入（import）的css、图片资源等。
b、use 属性，表示进行转换时，应该使用哪个 loader。
c、在 webpack 配置中定义 loader 时，要定义在 module.rules 中，而不是 rules。

示例：

> ``` 
> const path = require('path');
> const config = {           
>   output: {           
>        filename: 'my-first-webpack.bundle.js'             
>   },            
>   module: {         
>     rules: [          
>       { test: /\.txt$/, use: 'raw-loader' }   
>     ]          
>   }           
> };           
> module.exports = config;
> ``` 

上面的匹配的loader含义：当遇到匹配规则以.txt结尾，试用 raw-loader这个工具去解析一遍   

#### 4、插件(plugins)

插件可以实现复杂的：打包优化和压缩、重新定义环境中的变量等功能。插件接口功能极其强大，可以用来处理各种各样的任务。

> ``` 
> const HtmlWebpackPlugin = require('html-webpack-plugin'); // 通过 npm 安装
> const webpack = require('webpack'); // 用于访问内置插件
> const config = {
>  module: {         
>   rules: [
>        { test: /\.txt$/, use: 'raw-loader' }
> 		]
>   },
>   plugins: [         
>     new webpack.optimize.UglifyJsPlugin(),        
>     new HtmlWebpackPlugin({template: './src/index.html'})      
>   ]           
> };          
> module.exports = config;
> ``` 

#### 5、模式

平常在webpcak配置过程中，可以选择对应的启动模式，例如，新建一个文件，叫production.js:

> ```
> module.exports = {
>   mode: "production"
> }
> ```

### 实战

以下是一个vue项目的配置的整套逻辑

#### 创建一个vue项目

[vue官方文档地址](https://cn.vuejs.org/v2/guide/installation.html  "vue官方文档地址")

全局安装:

> 	vue-cli： npm install vue-cli -g

运行命令：

> vue init webpack testwebpack

按照步骤输入：

> ? Project name testwebpack
> ? Project description 按照步奏，输入名称：testwebpack
> ? Author kk 704798445@qq.com
> ? Vue build standalone
> ? Install vue-router? Yes
> ? Use ESLint to lint your code? No
> ? Set up unit tests No
> ? Setup e2e tests with Nightwatch? No
> ? Should we run `npm install` for you after the project has been created? (recommended) npm

会生成一个package.json文件，如下：

> ```
> {
>   "name": "testwebpack",
>   "version": "1.0.0",
>   "description": "testwebpack",
>   "author": "kk 704798445@qq.com",
>   "private": true,
>   "scripts": {
>     "dev": "webpack-dev-server --inline --progress --config build/webpack.dev.conf.js",
>     "start": "npm run dev",
>     "build": "node build/build.js"
>   },
>   "dependencies": {
>     "vue": "^2.5.2",
>     "vue-router": "^3.0.1"
>   },
>   "devDependencies": {
>     "autoprefixer": "^7.1.2",
>     "babel-core": "^6.22.1",
>     "babel-helper-vue-jsx-merge-props": "^2.0.3",
>     "babel-loader": "^7.1.1",
>     "babel-plugin-syntax-jsx": "^6.18.0",
>     "babel-plugin-transform-runtime": "^6.22.0",
>     "babel-plugin-transform-vue-jsx": "^3.5.0",
>     "babel-preset-env": "^1.3.2",
>     "babel-preset-stage-2": "^6.22.0",
>     "chalk": "^2.0.1",
>     "copy-webpack-plugin": "^4.0.1",
>     "css-loader": "^0.28.0",
>     "extract-text-webpack-plugin": "^3.0.0",
>     "file-loader": "^1.1.4",
>     "friendly-errors-webpack-plugin": "^1.6.1",
>     "html-webpack-plugin": "^2.30.1",
>     "node-notifier": "^5.1.2",
>     "optimize-css-assets-webpack-plugin": "^3.2.0",
>     "ora": "^1.2.0",
>     "portfinder": "^1.0.13",
>     "postcss-import": "^11.0.0",
>     "postcss-loader": "^2.0.8",
>     "postcss-url": "^7.2.1",
>     "rimraf": "^2.6.0",
>     "semver": "^5.3.0",
>     "shelljs": "^0.7.6",
>     "uglifyjs-webpack-plugin": "^1.1.1",
>     "url-loader": "^0.5.8",
>     "vue-loader": "^13.3.0",
>     "vue-style-loader": "^3.0.1",
>     "vue-template-compiler": "^2.5.2",
>     "webpack": "^3.6.0",
>     "webpack-bundle-analyzer": "^2.9.0",
>     "webpack-dev-server": "^2.9.1",
>     "webpack-merge": "^4.1.0"
>   },
>   "engines": {
>     "node": ">= 6.0.0",
>     "npm": ">= 3.0.0"
>   },
>   "browserslist": [
>     "> 1%",
>     "last 2 versions",
>     "not ie <= 8"
>   ]
> }
> ```

其中配置的npm命令有三条：

> ```
>   "scripts": {
>     "dev": "webpack-dev-server --inline --progress --config build/webpack.dev.conf.js",
>     "start": "npm run dev",
>     "build": "node build/build.js"
>   }
> ```

我们重点看一下： "dev": "webpack-dev-server --inline --progress --config build/webpack.dev.conf.js"，这条命令通过 webpack-dev-server这个组建，读取了 build/webpack.dev.conf.js 这个配置文件的内容，启动了一个server。

build目录下的：webpack.dev.conf.js 这个文件，引用了webpack.base.conf.js，其中webpack.base.conf.js主要内容如下：

> ```
> module.exports = { 
>	context: path.resolve(__dirname, '../'),
>   entry: {
>     app: './src/main.js'
>   },
>   output: {
>     path: config.build.assetsRoot,
>     filename: '[name].js',
>     publicPath: process.env.NODE_ENV === 'production'
>       ? config.build.assetsPublicPath
>       : config.dev.assetsPublicPath
>   },
>   resolve: {
>     extensions: ['.js', '.vue', '.json'],
>     alias: {
>       'vue$': 'vue/dist/vue.esm.js',
>       '@': resolve('src'),
>     }
>   },
>   module: {
>     rules: [
>       {
>         test: /\.vue$/,
>         loader: 'vue-loader',
>         options: vueLoaderConfig
>       },
>       {
>         test: /\.js$/,
>         loader: 'babel-loader',
>         include: [resolve('src'), resolve('test'), resolve('node_modules/webpack-dev-server/client')]
>       }
>       ......
>     ]
>   },
>   node: { ... }
}
> ```

以上文件配置，为一个标准的配置逻辑，包含了：入口（entry）、输出(output)、加载器(loader)、插件(plugins) 的完整的配置。

后续vue配置，可以看另外一个文档：[vue官方文档地址](https://cn.vuejs.org/v2/guide/installation.html  "vue官方文档地址");




