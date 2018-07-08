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
  module: {         
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

