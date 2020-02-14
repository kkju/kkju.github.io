##### redux-saga是什么？

1、正常的redux更新数据逻辑：
    createStore => dispatch action => reducer => get new state
2、更新包含一部调用：
    createStore => dispatch action =>[ ...redux-middleware] =>reducer => get new state

> redux-saga是一个redux的中间件，用于处理各种异步调用的。

###### 为何使用redux-saga？

** 原因： ** 控制器与更优雅的异步处理

 > 是一个用于管理应用程序 Side Effect（副作用，例如异步获取数据，访问浏览器缓存等）的 library，
 > 它的目标是让副作用管理更容易，执行更高效，测试更简单，在处理故障时更容易。

 > 使用了 ES6 的 Generator 功能

react-router-redux:

加强了React Router库中history这个实例，以允许将history中接受到的变化反应到state中去。



其他的几种中间件：

一、redux-thunk实现：
> 增强 dispatch, 判断是否传入一个执行函数，如果是，则执行

redux-thunk缺点：    
1.无尽的action.type的引用    
2.无尽的reducer与reduce的编写    
3.无尽的新建文件夹    

二、redux-promise

> 返回一个promise，在回调内去dispatch

缺点：    
1、回调嵌套太多  
2、由于这个中间件是直接拦截掉action, 并最终经过判断处理同样只会分发一次   
3、成功或者失败的时候需要额外的判断error    

三、redux-observable：
1.回调嵌套
