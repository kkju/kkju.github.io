### 冰鉴面试总结


#### absolute定位
相对于非 position:static 的第一个父级元素定位，如果都没有，则相对于 body 定位。

#### IE盒子模型和标准盒子模型

1、CSS css盒子模型 又称框模型 (Box Model) ，包含了元素内容（content）、内边距（padding）、边框（border）、外边距（margin）几个要素。

IE盒子模型 高度包括：padding、border,更像是 box-sizing : border-box; 其内容宽度还包含了border和padding。
> 解决办法就是：在html模板中加doctype声明。

#### box-sizing
> content-box：padding和border不被包含在定义的width和height之内。
盒子的实际宽度=设置的width+padding+border
> border-box：padding和border被包含在定义的width和height之内。
盒子的实际宽度=设置的width（padding和border不会影响实际宽度

#### 画三角形
```
<!DOCTYPE html>
<html>
  <head>
    <style>
        .triangle {
            width : 0;
            height: 0;
            border : 100px solid transparent;
            border-top : 100px solid blue; /*这里可以设置border的top、bottom、left、right四个方向的三角*/
        }
    </style>
  </head>
  <body>
    <div class="triangle"></div>
  </body>
</html>
```

#### VUEX

vuex作为vue的状态管理工具，类似于flux的东西。

#### 兄弟组建之间传递数据
eventBus、pubSub

#### localstorage

1.单页面监听storage事件
```
var orignalSetItem = localStorage.setItem;
localStorage.setItem = function(key, newValue) {
	var setItemEvent = new Event("setItemEvent");
	setItemEvent.key = key;
	setItemEvent.newValue = newValue;
	window.dispatchEvent(setItemEvent);
	orignalSetItem.apply(this, arguments);
}


window.addEventListener("setItemEvent", function(e) {              
	console.log(e.key + "~~~" + e.newValue)  
});
```

#### 事件代理

> 因为历史的原因，IE最开始实现实现事件流的方式：冒泡事件(event bubbling)，Netscape提出了另外一种事件流方式：事件捕获(event capturing)，不同的浏览器实现上有一些差别，用起来就有些繁琐。幸好现代浏览器都实现了W3C制定的"DOM2级事件"，"DOM2级事件"把事件流分为三个阶段，捕获阶段、目标阶段、冒泡阶段。

把事件绑定在父级元素上面，通过事件冒泡去实现的；


```
<!--省略html主体部分-->
<ul id="menu">
    <li><p><a>如果我被钗神翻牌，我就有一个成为大神的机会！</a></p></li>
    <li><p><a>如果我被钗神翻牌，我就有一个成为大神的机会！</a></p></li>
    <li><p><a>如果我被钗神翻牌，我就有一个成为大神的机会！</a></p></li>
    <!--中间省略1000000行-->
    <li><p><a>如果我被钗神翻牌，我就有一个成为大神的机会！</a></p></li>
</ul>


// 获取祖先节点(这儿就获取ul)，并为它添加一个click事件
document.getElementById("menu").addEventListener("click", function(e) {
  // 检查事件源e.targe是否为A
  if(e.target && e.target.nodeName.toUpperCase == "A") {
      console.log("钗神翻我牌，今晚就跟你走....");
  }
});
// event.stopPropagation();    

```

> 一般浏览器触发事件都是在冒泡阶段，如果你想在捕获阶段触发事件你可以设置addEventListener第三个参数为true，addEventListener("click", function(e){}, true)。

#### 获取dom元素的方式

通过ID获取（getElementById）
通过name属性（getElementsByName）
通过标签名（getElementsByTagName）
通过类名（getElementsByClassName）
通过选择器获取一个元素（querySelector），返回当前文档中第一个类名为 "myclass" 的元素
```
var el = document.querySelector(".myclass");
```

通过选择器获取一组元素（querySelectorAll），返回一个文档中所有的class为"note"或者 "alert"的div元素
```
var els = document.querySelectorAll("div.note, div.alert");
```

获取html的方法（document.documentElement）
document.documentElement是专门获取html这个标签的
获取body的方法（document.body）
document.body是专门获取body这个标签的。


#### 获取dom上数据

方法一： 但是只有公认的特性（非自定义的特性），例如id、title、style、align、className等

方法二：
```
let div = document.getElementById("my-div");
div.getAttribute("id"); // 获取id
div.getAttribute("title"); // 获取title
div.getAttribute("class"); // 获取元素的CSS类名，因为是传参数给getAttrbute函数，所以可以用class
div.getAttribute("dat-my-attribute"); // 获取自定义特性
div.setAttribute("id","anotherId"); // 设置id
div.removeAttribute("title"); // 删除title
````

#### BFC

BFC(Block formatting context)直译为"块级格式化上下文"。它是一个独立的渲染区域，只有Block-level box参与， 它规定了内部的Block-level Box如何布局，并且与这个区域外部毫不相干。


BFC布局规则：

> 内部的Box会在垂直方向，一个接一个地放置。    
> Box垂直方向的距离由margin决定。属于同一个BFC的两个相邻Box的margin会发生重叠    
> 每个元素的margin box的左边， 与包含块border box的左边相接触(对于从左往右的格式化，否则相反)。即使存在浮动也是如此。    
> BFC的区域不会与float box重叠。   
> BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。反之也如此。    
> 计算BFC的高度时，浮动元素也参与计算  


根元素
float属性不为none
position为absolute或fixed
display为inline-block, table-cell, table-caption, flex, inline-flex
overflow不为visible

**BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。反之也如此。**  

https://www.cnblogs.com/lhb25/p/inside-block-formatting-ontext.html


#### 计算css优先级

选择器的特殊性值表述为4个部分，用0,0,0,0表示。

1、ID选择器的特殊性值，加0,1,0,0。    
2、类选择器、属性选择器或伪类，加0,0,1,0。     
3、元素和伪元素，加0,0,0,1。     
4、通配选择器*对特殊性没有贡献，即0,0,0,0。    
5、最后比较特殊的一个标志!important（权重），它没有特殊性值，但它的优先级是最高的，为了方便记忆，可以认为它的特殊性值为1,0,0,0,0。     

特殊：
如果某个元素class（类选择器）的数量大于255个，不同的浏览器的表现不一致，包括但不限于以下浏览器认为class选择器的优先级会超过id选择器：
Firefox 52.0.2 (64 位)
IE 11

#### 清楚botton默认样式

width: 200px;
height: 50px;
background-color: red;
border-radius: 10px;
border: none;
outline: none;
-webkit-appearance: none;
-webkit-tap-highlight-color: rgba(0, 0, 0, 0);

#### 二分查找
```
// 非递归算法
function binary_search(arr, key) {
   var low = 0,
       high = arr.length - 1;
   while(low <= high){
       var mid = parseInt((high + low) / 2);
       if(key == arr[mid]){
           return  mid;
       }else if(key > arr[mid]){
           low = mid + 1;
       }else if(key < arr[mid]){
           high = mid -1;
       }else{
           return -1;
       }
   }
};
var arr = [1,2,3,4,5,6,7,8,9,10,11,23,44,86];
var result = binary_search(arr,10);
console.log(result); // 9 返回目标元素的索引值  
```

#### H5新增标签和属性
https://www.cnblogs.com/hz1124/p/6090835.html


#### js堆栈

js中的堆内存与栈内存
在js引擎中对变量的存储主要有两种位置，堆内存和栈内存。

和java中对内存的处理类似，栈内存主要用于存储各种基本类型的变量，包括Boolean、Number、String、Undefined、Null，以及对象变量的指针，这时候栈内存给人的感觉就像一个线性排列的空间，每个小单元大小基本相等。

而堆内存主要负责像对象Object这种变量类型的存储，如下图

> ![js堆栈](../static/imgs/js/js_stack.png "js堆栈")  

栈内存线性有序存储，容量小，系统分配效率高。而堆内存首先要在堆内存新分配存储区域，之后又要把指针存储到栈内存中，效率相对就要低一些了。

#### 浏览器渲染的逻辑
https://www.cnblogs.com/CandyManPing/p/6635008.html

#### Promise.all 原理

```
Promise.all = arr => {
	let aResult = []; //用于存放每次执行后返回结果
	return new _Promise(function(resolve, reject) {
		let i = 0;
		next(); //开始逐次执行数组中的函数
		function next() {
			arr[i].then(function(res) {
				aResult.push(res); //执行后返回的结果放入数组中
				i++;
				if (i == arr.length) { //如果函数数组中的函数都执行完，便把结果数组传给then
					resolve(aResult);
				} else {
					next();
				}
			})
		}
	})
}
```

#### https

> ![https](../static/imgs/http/https.gif "https公钥")  
