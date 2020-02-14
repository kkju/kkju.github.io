## npm

#### npm是一个包管理工具。开发者可以通过npm发布、安装包，达到代码共享和复用。


### 安装过程

1、根据package.json配置，得到所有包信息和包依赖关系；

2、根据依赖和npm的配置，去对于源拉取对于包代码，下载并解压

3、解压后文件分为：可执行文件或者源代码；

4、下载的npm包，会有一个package.json文件，指定了执行的入口文件；

### 定义可执行命令

npm允许在package.json中配置可执行的JavaScript定义的命令：

```js
	{ // ..."scripts":{"build":"node build.js"}}
```

1、可以通过： npm run build 执行，如果执行： node build.js


2、执行原理： 当运行 npm run * 时，会自动创建一个 shell, shell内容就是执行的对于的脚本。 


3、因此，只要shell可以执行的脚本，即可写成scripts放到packge.json配置中。

传入参数方式：


```js
    npm run create -- --app=test --router=1 --store=1
```


4、执行顺序

    串行执行： npm run x123 && npm run x1234

    并行执行： npm run script1.js & npm run script2.js


5、默认值： npm start 和 npm install 两个命令，不需要 run;


6、默认钩子

npm 默认提供下面这些钩子。prepublish，postpublishpreinstall，postinstallpreuninstall，postuninstallpreversion，postversionpretest，posttestprestop，poststopprestart，poststartprerestart，postrestart

prepublish这个钩子不仅会在npm publish命令之前运行，还会在npm install（不带任何参数）命令之前运行

npm 4 引入了一个新的钩子prepare，行为等同于prepublish，

npm 5 开始，prepublish将只在npm publish命令之前运行。


7、获取package.json中配置。

通过npm_package_前缀，npm 脚本可以拿到package.json里面的字段

```js

  // 配置内容 

  {"name":"foo","version":"1.2.5","scripts":{"view":"node view.js"}}

  // view.js内容
  console.log(process.env.npm_package_name); // foo
  console.log(process.env.npm_package_version); // 1.2.5

```

支持嵌套

```js

    // 示例

    "scripts":{"install":"foo.js"}

    // npm_package_scripts_install变量的值等于foo.js。

```