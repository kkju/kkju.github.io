# 面试题目总结第三部分 -- this和原型链

### 关于this

> this 表示的是函数调用时，对当前执行上下文的一个引用（或者叫活动记录）。包括调用栈（在哪儿被调用）、调用方式、传入参数；

绑定规则：    

> 1、默认独立函数调用，如下：

```
function a(){
  console.log(this.b);
}
var b = 1;
a(); // 输出 b;

/*
** 这个this指向的是全局作用域，
** 也是就是平常的window或者global;
*/
```
> 2、隐式绑定

```
function a(){
  console.log(this.b);
}
var c = {
  "b": "this is b",
  a: a
}
c.a(); // 输出： this is b;

/*
** 这个this指向的是引用的 c ，
** 隐式绑定会将引用放到这个上下文对象上去;
*/

// 下面是一个极端例子，请注意：

function foo(){
  console.log(this.a);
}

function actFoo(fn){
  fn();
}

var obj = {
  a: "obj's a",
  foo: foo
}

var a = "global";

actFoo(obj.foo); // 输出： global;

// 丢失了this，导致this指向了global;
// 所以才会有下面的显示绑定


```

> 3、显示绑定

```
function foo(){
  console.log(this.a);
}
var obj = {
  a: 2
};
var a = 1;
foo.call(obj); // 输出 2;

/*
** 当你传入一个原始值（bool, string, number）,
** 会被当做this的绑定对象原始值会被转换成对象形式
**（执行了New String(), new Boolen()），叫做装箱操作。
*/

// PS： 无法解决绑定丢失问题。
```

> 4、硬绑定

```
function foo(){
  console.log(this.a);
}
var obj = {
  a: 2
};
var bar = function(){
  foo.call(obj);
}
bar(); // 输出 2
var a = 1;
bar.call(global) // 输出 2;

// 平常常用的还有：apply、bind等

```

> 5、使用 new 进行绑定

使用new，例如 var obj = new Object(),会执行以下逻辑：            
a、创建或者构造一个全新对象；              
b、对象会被执行[[Prototype]]连接；                
c、新对象会绑定到函数调用的this;
d、如果函数没有返回其他对象，那么new表达式中的函数调用会自动返回这个新对象；
```
function foo(a){
  this.a = a;
}
var bar = new foo(2);
console.log(bar.a); // 2
```

> 6、优先级：

new ？ 显式绑定 > 隐式绑定 > 默认绑定

### 原型链

##### 函数与对象不同属性

> 所有 **引用类型（函数，数组，对象）** 都拥有 \__proto__ 属性（隐式原型）    
> 所有 **函数** 拥有 prototype 属性（显式原型）（仅限函数）    
> 原型对象：拥有 prototype 属性的对象，在定义函数时就被创建

上面描述内容示意图分别如下：      

```
// ps: __proto__不是规范属性，标准的属性是：[[Prototype]]

////////////  示例1：//////////////

var a = {};
console.log(a.prototype);  //undefined
console.log(a.__proto__);  //Object {}

var b = function(){}
console.log(b.prototype);  //b {}
console.log(b.__proto__);  //function() {}
```
解释：
> objects --> \__proto__   引用类型的属性    
> functions --> prototype  函数具有的属性

#### \__proto__指向谁？

```
///////////////////////// 示例2： ///////////////////////

/*1、字面量方式*/
var a = {};
console.log(a.__proto__);  //Object {}

console.log(a.__proto__ === a.constructor.prototype); //true

/*2、构造器方式*/
var A = function(){};
var a = new A();
console.log(a.__proto__); //A {}

console.log(a.__proto__ === a.constructor.prototype); //true

/*3、Object.create()方式*/
var a1 = {a:1}
var a2 = Object.create(a1);
console.log(a2.__proto__); //Object {a: 1}

console.log(a.__proto__ === a.constructor.prototype); //false（此处即为图1中的例外情况）
```
解释：
> a、字面量 \__proto__ 指向 function Object 的 prototype      
> b、构造器生成的对象 \__proto__ 指向 构造器函数的 prototype     
> c、Object.create 生成的对象 \__proto__ 指向 传入对象      

#### 原型链是什么？


```
//////////////////////////  示例3：///////////////////////
var A = function(){};
var a = new A();
console.log(a.__proto__); //A {}（即构造器function A 的原型对象）
console.log(a.__proto__.__proto__); //Object {}（即构造器function Object 的原型对象）
console.log(a.__proto__.__proto__.__proto__); //null
```
解释：
> 1、实际上，我们使用的是 A 的 prototype 来访问的它；     
> 2、a的 \__proto__ 指向的就是 A的 prototype       
> 3、A的 prototype 的 \__proto__ 指向 Object.prototype         
> 4、Object.prototype 指向 自己的构造器,
> 5、Object.prototype 的  \__proto__ ，是一个null，为整个链条终点。



#### js继承

##### 1、原型链继承
核心： 将父类的实例作为子类的原型
```
function Cat(){
}
Cat.prototype = new Animal();
Cat.prototype.name = 'cat';

//　Test Code
var cat = new Cat();
console.log(cat.name);
console.log(cat.eat('fish'));
console.log(cat.sleep());
console.log(cat instanceof Animal); //true
console.log(cat instanceof Cat); //true
```
特点：

非常纯粹的继承关系，实例是子类的实例，也是父类的实例
父类新增原型方法/原型属性，子类都能访问到
简单，易于实现

缺点：

要想为子类新增属性和方法，必须要在new Animal()这样的语句之后执行，不能放到构造器中
无法实现多继承
来自原型对象的所有属性被所有实例共享（来自原型对象的引用属性是所有实例共享的）（详细请看附录代码： 示例1）
创建子类实例时，无法向父类构造函数传参



##### 2、构造继承

核心：使用父类的构造函数来增强子类实例，等于是复制父类的实例属性给子类（没用到原型）

```
function Cat(name){
  Animal.call(this);
  this.name = name || 'Tom';
}

// Test Code
var cat = new Cat();
console.log(cat.name);
console.log(cat.sleep());
console.log(cat instanceof Animal); // false
console.log(cat instanceof Cat); // true
```

特点：
解决了1中，子类实例共享父类引用属性的问题
创建子类实例时，可以向父类传递参数
可以实现多继承（call多个父类对象）

缺点：
实例并不是父类的实例，只是子类的实例
只能继承父类的实例属性和方法，不能继承原型属性/方法
无法实现函数复用，每个子类都有父类实例函数的副本，影响性能

#####  组合继承
核心：通过调用父类构造，继承父类的属性并保留传参的优点，然后通过将父类实例作为子类原型，实现函数复用
```
function Cat(name){
  Animal.call(this);
  this.name = name || 'Tom';
}
Cat.prototype = new Animal();

// 感谢 @学无止境c 的提醒，组合继承也是需要修复构造函数指向的。

Cat.prototype.constructor = Cat;

// Test Code
var cat = new Cat();
console.log(cat.name);
console.log(cat.sleep());
console.log(cat instanceof Animal); // true
console.log(cat instanceof Cat); // true
```

特点：
弥补了方式2的缺陷，可以继承实例属性/方法，也可以继承原型属性/方法
既是子类的实例，也是父类的实例
不存在引用属性共享问题
可传参
函数可复用
缺点：

调用了两次父类构造函数，生成了两份实例（子类实例将子类原型上的那份屏蔽了）
推荐指数：★★★★（仅仅多消耗了一点内存）
