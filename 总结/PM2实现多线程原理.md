
### 二、PM2

#### 关于node的cluster

Node.js在单个线程中运行单个实例。 用户(开发者)为了使用现在的多核系统，有时候,用户(开发者)会用一串Node.js进程去处理负载任务。
cluster 模块允许简单容易的创建共享服务器端口的子进程。

> 每个worker进程通过使用child_process.fork()函数，基于IPC（Inter-Process Communication，进程间通信），实现与master进程间通信。    
> 当worker使用server.listen（...）函数时 ，将参数序列传递给master进程。如果master进程已经匹配workers，会将传递句柄给工人。如果master没有匹配好worker，那么会创建一个worker，再传递并句柄传递给worker。

```
const cluster = require('cluster');
const http = require('http');
const numCPUs = require('os').cpus().length;

if (cluster.isMaster) {
  console.log(`主进程 ${process.pid} 正在运行`);

  // 衍生工作进程。
  for (let i = 0; i < numCPUs; i++) {
    cluster.fork();
  }

  cluster.on('exit', (worker, code, signal) => {
    console.log(`工作进程 ${worker.process.pid} 已退出`);
  });
} else {
  // 工作进程可以共享任何 TCP 连接。
  // 在本例子中，共享的是一个 HTTP 服务器。
  http.createServer((req, res) => {
    res.writeHead(200);
    res.end('你好世界\n');
  }).listen(8000);

  console.log(`工作进程 ${process.pid} 已启动`);
}
```
#### PM2源码

> pm2包括 Satan进程、God Deamon守护进程、进程间的远程调用rpc、cluster等几个概念：     
> 如果不知道点西方文化，还真搞不清他的文件名为啥是 Satan 和 God     
> 撒旦（Satan），主要指《圣经》中的堕天使（也称堕天使撒旦），被看作与上帝的力量相对的邪恶、黑暗之源，是God的对立面。         
> Satan.js提供了程序的退出、杀死等方法，因此它是魔鬼；    
> God.js 负责维护进程的正常运行，当有异常退出时能保证重启。        
> God进程启动后一直运行，它相当于cluster中的Master进程，守护者worker进程的正常运行。    
