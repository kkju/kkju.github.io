### 字节跳动

#### 1、NaN特殊之处：
> NaN 属性是代表非数字值的特殊值。     
> 请使用 isNaN() 来判断一个值是否是数字。原因是 NaN 与所有值都不相等，包括它自己。    
> Number.NaN  

#### 2、Fetch

> 除了XMLHttpRequest对象来获取后台的数据之外，还可以使用一种更优的解决方案fetch

```
let url = 'https://segmentfault.com/a/1190000011433064';
fetch(url, {
  method: 'GET',
  headers: new Headers({
    'Content-Type': 'text/html' // 指定提交方式为表单提交
  }),
  // body: new URLSearchParams([["foo", 1],["bar", 2]]).toString()
}) // 返回一个Promise对象
  .then((res)=>{
    return res.text() // res.text()是一个Promise对象
  },(error)=>{
    console.log(error);
  })
  .then((res)=>{
    console.log(res) // res是最终的结果
  })

```

注意事项：

> 1、cookie传递，必须在header参数里面加上credentials: 'include'，才会如xhr一样将当前cookies带到请求中去   
> 2、fetch虽然底层，但是还是缺少一些常用xhr有的方法，比如能够取消请求（abort）方法，fetch在服务器返回4xx、5xx时是不会抛出错误的，这里需要手动通过，通过response中的ok字段和status字段来判断

PS： 取消方式

> Promise.race(iterable) 方法返回一个Promise对象, 只要 iterable 中任意一个Promise 被 resolve 或者 reject 后, 外部的Promise 就会以相同的值被 resolve 或者 reject；


```
var _fetch = (function(fetch){
  return function(url,options){
    var abort = null;
    var abort_promise = new Promise((resolve, reject)=>{
      abort = () => {
        reject('abort.');
        console.info('abort done.');
      };
    });
    var promise = Promise.race([
      fetch(url,options),
      abort_promise
    ]);
    promise.abort = abort;
    return promise;
  };
})(fetch);
```

#### 3、AMD和CMD区别

> AMD是（异步模块定义规范）制定定义模块的规则，模块和模块的依赖是异步加载的。    
> AMD通过define()函数定义一个模块：
```
define(?id, dependencies?, factory );
```

> AMD规范其实只有一个主要接口 define(id,dependencies,factory)，它要在声明模块的时候指定所有的依赖dependencies，并且还要当做形参传到factory中，对于依赖的模块提前执行，依赖前置

```
define("module", ["dep1", "dep2"], function(d1, d2) {
  return someExportedValue;
});
require(["module", "../file"], function(module, file) { /* ... */ });
```

优点：
  适合在浏览器环境异步加载
  并行加载多个模块
缺点：
   提高开发成本，代码阅读和书写比较困难
   不符合通用的模块思维方式，是一种妥协的实现


**不同点：**

1、明显不同的地方是：调用 和 声明依赖 的地方

> CMD标准倾向于在使用过程中提出依赖，就是不管代码写到哪突然发现需要依赖另一个模块，那就在当前代码用require引入就可以了，规范会帮你搞定预加载，你随便写就可以了。
> 但是AMD标准让你必须提前在头部依赖参数部分写好（没有写好？ 倒回去写好咯）。这就是最明显的区别。

2、对于依赖的模块，AMD 是提前执行，CMD 是延迟执行。

3、CMD 推崇依赖就近，AMD 推崇依赖前置。

4、AMD 的 API 默认是一个当多个用，CMD 的 API 严格区分，推崇职责单一。

#### 4、TCP和UDP

1、基本概念：

  TCP：Transmission Control Protocol
> 优势和缺点：可靠，稳定 TCP的可靠体现在TCP在传递数据之前，会有三次握手来建立连接，而且在数据传递时，有确认、窗口、重传、拥塞控制机制，在数据传完后，还会断开连接用来节约系统资源。 TCP的缺点： 慢，效率低，占用系统资源高，易被攻击 TCP在传递数据之前，要先建连接，这会消耗时间，而且在数据传递时，确认机制、重传机制、拥塞控制机制等都会消耗大量的时间，而且要在每台设备上维护所有的传输连接，事实上，每个连接都会占用系统的CPU、内存等硬件资源。 而且，因为TCP有确认机制、三次握手机制，这些也导致TCP容易被人利用，实现DOS、DDOS、CC等攻击。

  UDP(User Datagram Protocol)
>  快，比TCP稍安全 UDP没有TCP的握手、确认、窗口、重传、拥塞控制等机制，UDP是一个无状态的传输协议，所以它在传递数据时非常快。没有TCP的这些机制，UDP较TCP被攻击者利用的漏洞就要少一些。


2、使用权衡：
> a、TCP 和 UDP 是质量和实时性的权衡。   
> b、拿视频网站来说，你完全可以缓冲 20s 再播放，不会带来什么影响，但如果画面有马赛克之类的东西出现肯定是不好的，所以用 TCP。    
> c、而对于视频聊天，如果缓冲 5s，相信整个聊天已经没法愉快的进行了，而这时出现一些画面质量的损失也可以被接受，所以用 UDP。

3、TCP三次握手、四次挥手

> ![TCP三次握手](../static/imgs/tcp/tcp.png "TCP三次握手")  

> 1、同步SYN，在连接建立时用来同步序号。当SYN=1，ACK=0，表明是连接请求报文，若同意连接，则响应报文中应该使 SYN=1，ACK=1；      
> 2、确认ACK，仅当ACK=1时，确认号字段才有效。TCP规定，在连接建立后所有报文的传输都必须把ACK置1；

整个过程：
  客户端发起链接：发送一个SYN包给服务器，SYN=1 seq=x，进入发起SYN_SEND状态;
  服务器端收到数据，发送SYN+ACK包，SYN=1，ACK=x+1，seq=y，进入 SYN_Rev状态；
  客户端收到数据，发送ACK包，ACK=y+1,seq=z;
  双方进入ESTABLISH状态


> ![TCP四次挥手](../static/imgs/tcp/tcp_bye.gif "TCP四次挥手")  


整个过程：
  客户端发起断开链接： Fin=1,ACK=z,seq=x;
  服务器收到请求，返回ACK包：ACK=x+1,Seq=z;
  服务器也发送断开链接的Fin+ACK包：Fin=1,ACK=x,seq=y;
  客服端响应ACK：ACK=y+1;Seq=y;


#### 隐形类型转换

链接地址： https://www.h5jun.com/post/why-false-why-true.html

注意：[] == false  为true;


#### 5、http缓存

##### 强制缓存
对于强制缓存来说，响应header中会有两个字段来标明失效规则（Expires/Cache-Control）

a、Expires 失效时间点

b、Cache-Control

> 1）private: 客户端可以缓存   
> 2）public:  客户端和代理服务器都可缓存（前端的同学，可以认为public和private是一样的）     
> 3）max-age=xxx:  缓存的内容将在 xxx 秒后失效      
> 4）no-cache: ***需要使用对比缓存来验证缓存数据***     
> 5）no-store: 所有内容都不会缓存，强制缓存，对比缓存都不会触发     

#### 对比缓存
顾名思义，需要进行比较判断是否可以使用缓存。
浏览器第一次请求数据时，服务器会将缓存标识与数据一起返回给客户端，客户端将二者备份至缓存数据库中。
再次请求数据时，客户端将备份的缓存标识发送给服务器，服务器根据缓存标识进行判断，判断成功后，返回304状态码，通知客户端比较成功，可以使用缓存数据。




##### 1、Last-Modified/If-Modified-Since规则

Last-Modified：
服务器在响应请求时，告诉浏览器资源的最后修改时间。

If-Modified-Since：
再次请求服务器时，通过此字段通知服务器上次请求时，服务器返回的资源最后修改时间。
服务器收到请求后发现有头If-Modified-Since 则与被请求资源的最后修改时间进行比对。
若资源的最后修改时间大于If-Modified-Since，说明资源又被改动过，则响应整片资源内容，返回状态码200；
若资源的最后修改时间小于或等于If-Modified-Since，说明资源无新修改，则响应HTTP 304，告知浏览器继续使用所保存的cache。        

###### 2、Etag/If-None-Match规则（优先级高于Last-Modified/If-Modified-Since）

Etag：
服务器资源的唯一标识符, 浏览器可以根据ETag值缓存数据, 节省带宽. 如果资源已经改变, etag可以帮助防止同步更新资源的相互覆盖. ETag 优先级比 Last-Modified 高.

If-None-Match：
再次请求服务器时，通过此字段通知服务器客户段缓存数据的唯一标识。
服务器收到请求后发现有头If-None-Match 则与被请求资源的唯一标识进行比对，
不同，说明资源又被改动过，则响应整片资源内容，返回状态码200；
相同，说明资源无新修改，则响应HTTP 304，告知浏览器继续使用所保存的cache。

#### 6、手写一个bind

```
if (!Function.prototype.bind) {
  Function.prototype.bind = function(oThis) {
    if (typeof this !== 'function') {
      // closest thing possible to the ECMAScript 5
      // internal IsCallable function
      throw new TypeError('Function.prototype.bind - what is trying to be bound is not callable');
    }

    var aArgs   = Array.prototype.slice.call(arguments, 1),
        fToBind = this,
        fNOP    = function() {},
        fBound  = function() {
          // this instanceof fNOP === true时,说明返回的fBound被当做new的构造函数调用
          return fToBind.apply(this instanceof fNOP
                 ? this
                 : oThis,
                 // 获取调用时(fBound)的传参.bind 返回的函数入参往往是这么传递的
                 aArgs.concat(Array.prototype.slice.call(arguments)));
        };

    // 维护原型关系
    if (this.prototype) {
      // Function.prototype doesn't have a prototype property
      fNOP.prototype = this.prototype;
    }
    // 下行的代码使fBound.prototype是fNOP的实例,因此
    // 返回的fBound若作为new的构造函数,new生成的新对象作为this传入fBound,新对象的__proto__就是fNOP的实例
    fBound.prototype = new fNOP();

    return fBound;
  };
}
```

#### 7、bind相关概念

> a、在 javascript 中，call 和 apply 都是为了改变某个函数运行时的上下文（context）而存在的，换句话说，就是为了改变函数体内部 this 的指向。
> b、JavaScript 的一大特点是，函数存在「定义时上下文」和「运行时上下文」以及「上下文是可以改变的」这样的概念。

例子：

```
function fruits() {}

fruits.prototype = {
    color: "red",
    say: function() {
        console.log("My color is " + this.color);
    }
}

var apple = new fruits();
apple.say();    //My color is red

// callable
banana = {
    color: "yellow"
}
apple.say.call(banana);     //My color is yellow
apple.say.apply(banana);    //My color is yellow
apple.say.bind(banana); // My color is yellow
```


** 总结：**

> 1、apply 、 call 、bind 三者都是用来改变函数的this对象的指向的；      
> 2、apply 、 call 、bind 三者第一个参数都是this要指向的对象，也就是想指定的上下文；       
> 3、apply 、 call 、bind 三者都可以利用后续参数传参；bind 是返回对应函数，便于稍后调用；apply 、call 则是立即调用 。

#### 8、进程和线程区别：

> 进程是操作系统资源分配的基本单位，而线程是任务调度和执行的基本单位

> 进程和线程都是一个时间段的描述，是CPU工作时间段的描述。

> 进程就是包换上下文切换的程序执行时间总和 = CPU加载上下文+CPU执行+CPU保存上下文

> 进程的颗粒度太大，每次都要有上下的调入，保存，调出。如果我们把进程比喻为一个运行在电脑上的软件，那么一个软件的执行不可能是一条逻辑执行的，必定有多个分支和多个程序段，就好比要实现程序A，实际分成 a，b，c等多个块组合而成。那么这里具体的执行就可能变成：

> 程序A得到CPU =》CPU加载上下文，开始执行程序A的a小段，然后执行A的b小段，然后再执行A的c小段，最后CPU保存A的上下文。

> 这里a，b，c的执行是共享了A的上下文，CPU在执行的时候没有进行上下文切换的。这里的a，b，c就是线程，也就是说线程是共享了进程的上下文环境，的更为细小的CPU时间段。


进程是具有一定独立功能的程序关于某个数据集合上的一次运行活动，进程是系统进行资源分配和调度的一个独立单位。

线程是进程的一个实体，是CPU调度和分派的基本单位，它是比进程更小的能独立运行的基本单位。

相对进程而言，线程是一个更加接近于执行体的概念，它可以与同进程中的其他线程共享数据，但拥有自己的栈空间，拥有独立的执行序列。

#### 9、快速排序

```
function quickSort(arr){
  if(arr.length <=1){
    return arr;
  }
  var lenMiddle = Math.floor(arr.length/2);
  // var middle = arr[lenMiddle];
  var middle = arr.splice(lenMiddle, 1)[0]; // 会有空数组
  console.log(middle, arr[lenMiddle]);
  var left = [],
      right = [];
  for(var i = 0; i < arr.length; i++){
      if(arr[i] < middle){
        left.push(arr[i]);
      }else{
        right.push(arr[i]);
      }
  }
  return quickSort(left).concat([middle], quickSort(right));
};

var a = quickSort([1,2,3,4,5,6,7,8,9,10]);
console.log(a);
var b = quickSort([10,2,4,3,5,6,7,11,9,8]);
console.log(b);
```

#### Event Loop

任务队列优先级：promise.Trick()>promise的回调>setTimeout>setImmediate
```
setTimeout(() => console.log('a'), 0);
var p = new Promise((resolve) => {
  console.log('b');
  resolve();
});
p.then(() => console.log('c'));
p.then(() => console.log('d'));
console.log('e');
// 结果：b e c d a

async function async1() {
    console.log("a");
    await  async2(); //执行这一句后，await会让出当前线程，将后面的代码加到任务队列中，然后继续执行函数后面的同步代码
    console.log("b");

}
async function async2() {
   console.log( 'c');
}
console.log("d");
setTimeout(function () {
    console.log("e");
},0);
async1();
new Promise(function (resolve) {
    console.log("f");
    resolve();
}).then(function () {
    console.log("g");
});
console.log('h');
// 谁知道为啥结果不一样？？？？？？？？？？？？？
// 直接在控制台中运行结果：      d a c f h g b e
// 在页面的script标签中运行结果：d a c f h b g e
```
