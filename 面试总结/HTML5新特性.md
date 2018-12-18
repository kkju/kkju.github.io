1、语义化标签；

header、footer、nav、aside、dialog

2、增强型表单

color、date、datetime、datetime-local、email、month、number、range、search、tel、time、url、week

3、视频和音频

4、Canvas绘图

```
<script>
	var c=document.getElementById("myCanvas");
	var ctx=c.getContext("2d");
	ctx.fillStyle="#FF0000";
	ctx.fillRect(0,0,150,75);
</script> 
```


5、SVG绘图 使用xml 描述 2D 图形的语言

SVG 是一种使用 XML 描述 2D 图形的语言。

Canvas 通过 JavaScript 来绘制 2D 图形。

SVG 基于 XML，这意味着 SVG DOM 中的每个元素都是可用的。您可以为某个元素附加 JavaScript 事件处理器。

在 SVG 中，每个被绘制的图形均被视为对象。如果 SVG 对象的属性发生变化，那么浏览器能够自动重现图形。

Canvas 是逐像素进行渲染的。在 canvas 中，一旦图形被绘制完成，它就不会继续得到浏览器的关注。如果其位置发生变化，那么整个场景也需要重新绘制，包括任何或许已被图形覆盖的对象。


6、地理定位

7、拖放API
```
<img draggable="true">
```

8、Web Worker
```
if(typeof(Worker)!=="undefined"){
  let w = new Worker("demo_workers.js");
}else{
  //抱歉! Web Worker 不支持
}

w.onmessage=function(event){
	document.getElementById("result").innerHTML=event.data;
};
```
service worker
```
<script>
// 检查浏览器是否对 serviceWorker 有原生支持
if ('serviceWorker' in navigator) {
  // 有原生支持时，在页面加载后开启新的 Service Worker 线程，从而优化首屏加载速度
  window.addEventListener('load', function() {
  // register 方法里第一个参数为 Service Worker 要加载的文件；第二个参数 scope 可选，用来指定 Service Worker 控制的内容的子目录
    navigator.serviceWorker.register('./ServiceWorker.js').then(function(registration) {
      // Service Worker 注册成功
      console.log('ServiceWorker registration successful with scope: ', registration.scope);
    }).catch(function(err) {
      // Service Worker 注册失败
      console.log('ServiceWorker registration failed: ', err);
    });
  });
}
</script>
```

9、Web Storage
localStorage - 没有时间限制的数据存储
sessionStorage - 针对一个 session 的数据存储, 当用户关闭浏览器窗口后，数据会被删除。
10、WebSocket

var ws = new WebSocket("ws://localhost:9998/echo");
onopen、onerror、onmessage、onclose