### 一、KOA
> 通过利用 async 函数，Koa 帮你丢弃回调函数，并有力地增强错误处理。       
> Koa 并没有捆绑任何中间件， 而是提供了一套优雅的方法，帮助您快速而愉快地编写服务端应用程序。      
> koa的洋葱模型，平常的模块主要使用 use 调用 middleware    
koa做了什么：
* 1.封装了ctx
* 2.中间件
* 3.解析请求体（koa-bodyparser)
* 4.解析路由（koa-router）

> http.server对象是一个EventEmitter，有一个重要的事件就是request，每当服务器收到一个请求后，便会触发request事件。

#### koa中间件

> 平常我们开发遇到的很多都是koa中间件    

1、koa添加中间件的方式是app.use(fn)。app就是koa模块的实例。     
2、compose方法就是用来链式执行中间件的。

> 以 koa-bodyparser 为例子：

1、koa-bodyparser中间件是将我们的 post 请求和表单提交的查询字符串转换成对象；
2、Koa 使用中间件的过程就是一个典型的 “装饰器” 模式， 并且Koa 具备 “洋葱模型” 和异步特点。

#### 实现一个简单的koa

##### 1、封装node http Server
```
const http = require("http");
let server = http.createServer((req, res)=>{
    res.writeHead(200);  
    res.end("hello world");
});
server.listen(3000, ()=>{
   console.log("listen on 3000");  
});
```

##### 2、构造resquest, response, context对象

###### a、封装类
```
/**
 application.js
**/
const http = require("http");
Class Application{
    constructor() {
        this.callbackFunc;
    }
     /**
      * 开启http server并传入callback
      */
     listen(...args) {
         let server = http.createServer(this.callback());
         server.listen(...args);
     }
     /**
     * 挂载回调函数
     * @param {Function} fn 回调处理函数
     */
     use(fn) {
         this.callbackFunc = fn;
     }
     /**
      * 获取http server所需的callback函数
      * @return {Function} fn
      */
     callback() {
         return (req, res) => {
             this.callbackFunc(req, res);
         }
     };

}
module.exports = Application;
```
###### b、调用的js
```
const Application = require("./application");
const app = new Application();
app.use((req, res) => {
    res.writeHead(200);
    res.end("hello world");
});
app.listen(300, ()=>{
  console.log('listening on 3000');
});
```

###### c、封装request
```
// request.js
let url = require('url');
module.exports = {

    get query() {
        return url.parse(this.req.url, true).query;
    }
};
```

###### c、封装response
```
// request.js
let url = require('url');
module.exports = {
      get body() {
          return this._body;
      },

      /**
       * 设置返回给客户端的body内容
       *
       * @param {mixed} data body内容
       */
      set body(data) {
          this._body = data;
      },

      get status() {
          return this.res.statusCode;
      },

      /**
       * 设置返回给客户端的stausCode
       *
       * @param {number} statusCode 状态码
       */
      set status(statusCode) {
          if (typeof statusCode !== 'number') {
              throw new Error('statusCode must be a number!');
          }
          this.res.statusCode = statusCode;
      }
};
```
###### c、创建context
```
module.exports = {

    get query() {
        return this.request.query;
    },

    get body() {
        return this.response.body;
    },

    set body(data) {
        this.response.body = data;
    },

    get status() {
        return this.response.status;
    },

    set status(statusCode) {
        this.response.status = statusCode;
    }
};
```
精简版本：
```
let proto = {};
// 为proto名为property的属性设置setter
function delegateSet(property, name) {
    proto.__defineSetter__(name, function (val) {
        this[property][name] = val;
    });
}
// 为proto名为property的属性设置getter
function delegateGet(property, name) {
    proto.__defineGetter__(name, function () {
        return this[property][name];
    });
}
// 定义request中要代理的setter和getter
let requestSet = [];
let requestGet = ['query'];
// 定义response中要代理的setter和getter
let responseSet = ['body', 'status'];
let responseGet = responseSet;
requestSet.forEach(ele => {
    delegateSet('request', ele);
});
requestGet.forEach(ele => {
    delegateGet('request', ele);
});
responseSet.forEach(ele => {
    delegateSet('response', ele);
});
responseGet.forEach(ele => {
    delegateGet('response', ele);
});
```
修改过后的 application ：
```
// application.js
let http = require('http');
let context = require('./context');
let request = require('./request');
let response = require('./response');
class Application {
/**
 * 构造函数
 */
constructor() {
    this.callbackFunc;
    this.context = context;
    this.request = request;
    this.response = response;
}
/**
 * 开启http server并传入callback
 */
listen(...args) {
    let server = http.createServer(this.callback());
    server.listen(...args);
}
/**
 * 挂载回调函数
 * @param {Function} fn 回调处理函数
 */
use(fn) {
    this.callbackFunc = fn;
}
/**
 * 获取http server所需的callback函数
 * @return {Function} fn
 */
callback() {
    return (req, res) => {
        let ctx = this.createContext(req, res);
        let respond = () => this.responseBody(ctx);
        this.callbackFunc(ctx).then(respond);
    };
}
/**
 * 构造ctx
 * @param {Object} req node req实例
 * @param {Object} res node res实例
 * @return {Object} ctx实例
 */
createContext(req, res) {
    // 针对每个请求，都要创建ctx对象
    let ctx = Object.create(this.context);
    ctx.request = Object.create(this.request);
    ctx.response = Object.create(this.response);
    ctx.req = ctx.request.req = req;
    ctx.res = ctx.response.res = res;
    return ctx;
}
/**
 * 对客户端消息进行回复
 * @param {Object} ctx ctx实例
 */
responseBody(ctx) {
    let content = ctx.body;
    if (typeof content === 'string') {
        ctx.res.end(content);
    }
    else if (typeof content === 'object') {
        ctx.res.end(JSON.stringify(content));
    }
}
}

```
修改app.js
```
let simpleKoa = require('./application');
let app = new simpleKoa();
app.use(async ctx => {
    ctx.body = 'hello ' + ctx.query.name;
});
app.listen(3000, () => {
    console.log('listening on 3000');
});
```

##### 3、中间件机制
```
// 修改application.js
...
Class application{
  constructor(){
    this.middleware = [];
  }
  use(middleware){
    this.middleware.push(middleware);
  }
  ...
  compose(){
    // 将middlewares合并为一个函数，该函数接收一个ctx对象
    return async ctx => {

         function createNext(middleware, oldNext) {
             return async () => {
                 await middleware(ctx, oldNext);
             }
         }

         let len = this.middlewares.length;
         let next = async () => {
             return Promise.resolve();
         };
         for (let i = len - 1; i >= 0; i--) {
             let currentMiddleware = this.middlewares[i];
             next = createNext(currentMiddleware, next);
         }

         await next();

     };
  }

  callback() {
      return (req, res) => {
          let ctx = this.createContext(req, res);
          let respond = () => this.responseBody(ctx);
          let fn = this.compose();
          return fn(ctx).then(respond);
      };
  }


}
module.exports = application;
```

##### 4、错误处理

```
Class application(){
  ...
  callback() {
       return (req, res) => {
           let ctx = this.createContext(req, res);
           let respond = () => this.responseBody(ctx);
           let onerror = (err) => this.onerror(err, ctx);
           let fn = this.compose();
           // 在这里catch异常，调用onerror方法处理异常
           return fn(ctx).then(respond).catch(onerror);
       };
   }

   // ...

   /**
    * 错误处理
    * @param {Object} err Error对象
    * @param {Object} ctx ctx实例
    */
   onerror(err, ctx) {
       if (err.code === 'ENOENT') {
           ctx.status = 404;
       }
       else {
           ctx.status = 500;
       }
       let msg = err.message || 'Internal error';
       ctx.res.end(msg);
       // 触发error事件
       this.emit('error', err);
   }
}
```
