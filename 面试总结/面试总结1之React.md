# 面试题目总结第二部分 -- React

### 一、React

#### 1、基本概念

##### 1）项目描述

1、用来构建UI的 JavaScript库    
2、React 不是一个 MVC 框架，仅仅是视图（V）层的库
2、使用组件化开发方式，符合现代Web开发的趋势    
3、技术成熟，社区完善，配件齐全，适用于大型Web项目（生态系统健全）

** 优势： **        
（1）可组合（Composeable）：一个组件易于和其它组件一起使用，或者嵌套在另一个组件内部。
如果一个组件内部创建了另一个组件，那么说父组件拥有（own）它创建的子组件，
通过这个特性，一个复杂的UI可以拆分成多个简单的UI组件；

（2）可重用（Reusable）：每个组件都是具有独立功能的，它可以被使用在多个UI场景；

（3）可维护（Maintainable）：每个小的组件仅仅包含自身的逻辑，更容易被理解和维护；

##### 2）虚拟DOM（Virtual DOM）

> React将DOM抽象为虚拟DOM，虚拟DOM其实就是用一个对象来描述DOM，通过对比前后两个对象的差异，
> 最终只把变化的部分重新渲染，提高渲染的效率

剪裁方式：深度优先遍历 + 差异类型比较 => 再渲染dom

##### 3）Diff算法（虚拟DOM的加速器，提升React性能的法宝）

> 1、key的重要性：如果没有key，所有兄弟节点，都会重新渲染；   
> 2、尽可能的减少数组index作为key，数组中插入元素的等操作时，会使得效率底下   

#### 2、React中常见模式

> 1、混入（mixins） 常见于：高阶组件（HOC）

什么是高阶组件：一个高阶组件只是一个包装了另外一个 React 组件的 React 组件。

```
const EnhancedComponent = higherOrderComponent(WrappedComponent);
```
> 可以实现的功能：
> a、属性代理（Props Proxy）：高阶组件操控传递给 WrappedComponent 的 props；   
> b、反向继承（Inheritance Inversion）：高阶组件继承（extends）WrappedComponent。  

作用：

1、代码复用，逻辑抽象，抽离底层准备（bootstrap）代码    
2、渲染劫持     
3、State 抽象和更改     
4、Props 更改     

最简单示例：
1、Props Proxy （PP）
```
function ppHOC(WrappedComponent) {
  return class PP extends React.Component {
    render() {
      return <WrappedComponent {...this.props}/>
    }
  }
}
```

> 2、组合模式 常见于：各种模块

> 3、工厂模式 ReactComponent 加工成不同的componet

#### 3、ReactClass
对比 ES5 写法: var MyComponent = React.createClass({})，ES6写法：class MyComponent extends React.Component;

#### 4、redux
> 1、redux用于解决多个UI组件之间的数据共享问题。
> 2、严格数据只能在一个方向上流动。
> 3、所有的数据（比如state）被保存在一个被称为store的容器中 → 在一个应用程序中只能有一个。store本质上是一个状态树，保存了所有对象的状态。  
> 4、“model view update” 原则

##### redux微型源码
```
const changeStateFn = (state, action) => {
	console.log(state, action);
	switch(action.type){
		case "PLAY_VIDEO":
			return {
				...state,
				"title": action.title
			};
		    break;
		case "NEXT_VIDEO":
			return {
				...state,
				"title": action.title
			};
		   break;
		case "STOP_VIDEO":
			return {
				...state,
				"title": action.title
			};
		    break;
		default:
			return state;
			break;
	}
}

const createStore = (reducer) => {
	let state = {};

	let listeners = [];

	const getState = () => {
		return state;
	}
	const dispatch = (action) => {
		// 需要更新状态
		state = reducer(state, action);
		// 调用所有监听
		listeners.forEach((listener) => {
		   listener();
		})
	}

	const subscribe = (listener) =>{
		listeners.push(listener);
	}

	return {getState, dispatch, subscribe}
}

const store = createStore(changeStateFn);

const renderApp = (newState, oldState) => {
	if (newState === oldState) {
		return ;
	}
	console.log("render");
};

let oldState = store.getState();

// 订阅事件
store.subscribe(() => {
	// 获取最新状态
	const newState = store.getState();
	// 渲染
	renderApp(newState, oldState);
	// 更新老状态
	oldState = newState;
	console.log("oldState:", oldState);
});

store.dispatch({"type": "PLAY_VIDEO", "title": "播放中"});
setTimeout(()=>{
  store.dispatch({"type": "PLAY_VIDEO", "title": "播放结束"});
}, 100);
```

##### redux-react

> react-redux库提供Provider组件通过context方式向应用注入store，然后可以使用connect高阶方法，获取并监听store，然后根据store state和组件自身props计算得到新props，注入该组件，并且可以通过监听store，比较计算出的新props判断是否需要更新组件。     

#### react生命周期

生命周期图如下：

> ![react生命周期图](../static/imgs/react/react_life.png "react生命周期图")

> ![react生命周期图](../static/imgs/react/react_life1.jpg "react生命周期图")  


> a、首先，我们看一下一个组件的构造：

```
import React, { Component } from "react";

Class Test extends Component{
  constructor(props, context){
    super(props, context);
    this.state = {
      // 定义state
    }
  }

  getDefaultProps(){}
  getInitialState(){}
  componentWillMount(){}
  componentDidMount(){}
  componentWillReceiveProps(nextProps){}
  shouldComponentUpdate(nextProps, nextState){}
  componentWillUpdate(nextProps, nextState){}
  componentDidUpdate(prevProps, prevState){}

  render(){
    return(<div></div>)
  }

  componentWillUnmout(){}

}
```
理解：
> 1、组建经历constructor初始化数据；    
> 2、进入componentWillMount 组件即将挂载 逻辑     
> 3、当组件第一次完成渲染，会触发 componentDidMount， 表示dom节点已经生成，可以使用调用的异步函数，进行数据调用，并且将返回数据setState;      
> 4、componentWillReceiveProps 是在父组件改变了传入的 props 的时候，会首选调用，平常使用：会用nextProps和this.props对比判断，然后setState;       
> 5、shouldComponentUpdate 可以用于在父组件更新后，导致子组件重新渲染都得逻辑，如果不重新渲染子组件，可以在此部分进行 return  false 处理，不做子组件更新;       
> 6、componentWillUpdate 是在 shouldComponentUpdate return false的时候调用；      
> 7、redner 会在react更新完成后，根据diff算法，会拿到一个差异最小的dom借点书，进行渲染
> 8、render完成后，会进入 componentDidUpdate
> 9、当卸载组件的时候，会促发： componentWillUnmout ，可以用于销毁组件的时候，某些操作还在进行的时候，作为判断使用：

```
componentDidMount(){
  this.isMount = true;
  axios.post().then((res)=>{
    this.isMount && this.setState({
      res: res
    });
  });
}
componentWillUnmout(){
  this.isMount = false;
}
```

更新顺序：
> 1、react的组件挂载及render过程中，最底层的子组件是最先完成挂载及更新的。   
> constructor()构造函数、componentWillMount执行顺序：顶层父组件--子组件--子组件--...--底层子组件    
> 2、render、componentDidMount顺序：底层子组件--子组件--子组件--...--顶层父组件

update phases同理


##### diff算法


tree diff

React 对树的算法进行了简洁明了的优化，即对树进行分层比较，两棵树只会对同一层次的节点进行比较。

component diff

1、先判断是否为同类型组建，如果是，按照原策略继续比较 virtual DOM tree。

2、不同组建，直接删除，重新渲染；

element diff

3、当节点处于同一层级时，React diff 提供了三种节点操作，分别为：INSERT_MARKUP（插入）、MOVE_EXISTING（移动）和 REMOVE_NODE（删除）。
> 先对新集合的节点进行循环遍历，for (name in nextChildren)，通过唯一 key 可以判断新老集合中是否存在相同的节点，if (prevChild === nextChild)，如果存在相同节点，则进行移动操作，但在移动前需要将当前节点在老集合中的位置与 lastIndex 进行比较，if (child.\_mountIndex < lastIndex)，则进行节点移动操作，否则不执行该操作。

##### react的生命周期

###### 初始化

当组件在客户端被实例化，第一次被创建时，以下方法依次被调用：

1、getDefaultProps

对于每个组件实例来讲，这个方法只会调用一次，该组件类的所有后续应用，getDefaultPops 将不会再被调用，其返回的对象可以用于设置默认的 props(properties的缩写) 值。


2、getInitialState

对于组件的每个实例来说，这个方法的调用有且只有一次，用来初始化每个实例的 state，在这个方法里，可以访问组件的 props。每一个React组件都有自己的 state，其与 props 的区别在于 state只存在组件的内部，props 在所有实例中共享。
es6写法里面，在constructor内实现

3、componentWillMount

该方法在首次渲染之前调用，也是再 render 方法调用之前修改 state 的最后一次机会。

4、render

该方法会创建一个虚拟DOM，用来表示组件的输出。    
a.只能通过 this.props 和 this.state 访问数据（不能修改）    
b.可以返回 null,false 或者任何React组件    
c.只能出现一个顶级组件，不能返回一组元素    
d.不能改变组件的状态    
e.不能修改DOM的输出

5、componentDidMount
该方法不会在服务端被渲染的过程中调用。该方法被调用时，已经渲染出真实的 DOM，可以用来执行数据调用。


##### 存在期间

componentWillReceiveProps
组件的 props 属性可以通过父组件来更改，这时，componentWillReceiveProps 将来被调用。可以在这个方法里更新 state,以触发 render 方法重新渲染组件。

shouldComponentUpdate
如果你确定组件的 props 或者 state 的改变不需要重新渲染，可以通过在这个方法里通过返回 false 来阻止组件的重新渲染，返回 false 则不会执行 render 以及后面的 componentWillUpdate，componentDidUpdate 方法。


componentWillUpdate
这个方法和 componentWillMount 类似，在组件接收到了新的 props 或者 state 即将进行重新渲染前，componentWillUpdate(object nextProps, object nextState) 会被调用，注意不要在此方面里再去更新 props 或者 state。

componentDidUpdate
这个方法和 componentDidMount 类似，在组件重新被渲染之后，componentDidUpdate(object prevProps, object prevState) 会被调用。可以在这里访问并修改 DOM。


##### 销毁时

componentWillUnmount
每当React使用完一个组件，这个组件必须从 DOM 中卸载后被销毁，此时 componentWillUnmout 会被执行，完成所有的清理和销毁工作，在 componentDidMount 中添加的任务都需要再该方法中撤销，如创建的定时器或事件监听器。
