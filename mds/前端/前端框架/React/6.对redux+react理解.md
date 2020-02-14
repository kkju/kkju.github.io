### 个人对redux和react的一些理解

#### 何为Redux

> 官方说法：一个javascript的带状态的数据容器

> 相关概念：reducer、action、subscribe、dispatch

1、reducer为更新数据的一个方法，需要将新数据传入，并且传入一个带有Type属性的一个action
2、用户通过dispatch发出一个action，自己增加数据逻辑，返回一个新的state
3、在数据更新的时候，执行 subscribe 的对应listener

#### Redux和react如何结合的

##### 新的概念-redux-react

关键api： Provider + Connect

** Provider实际上是一个拦截器作用 **

1、将整个应用作为自己的子节点，将生成的store，作为prop传下去    
2、并将其挂载到自己的 context 上面，用于后续 Connect 使用      

> 何为 context ？
> 你可以把它当做一个跨级可以访问的一个父节点上面的一个全部子节点上可以访问的一个 ** 上下文 ** 或者叫做 基于生产者消费者模式提供父子组件跨级通信的一种方式。

**  Connect实际上是一个高阶组件 **

> 1、将传入的 的mapStateToProps, mapDispatchToProps 合并成新的 props;    
> 2、 会把传入下来的props里面的store或者context中的store，设置为自己的state;
```
....
class Connect extends Component {
    constructor(props, context) {
        super(props, context)
        this.store = props.store || context.store;
        const storeState = this.store.getState()
        this.state = { storeState }
    }
    ....
}
```
> 2、返回了一个新的函数，这个函数返回了一个 component, 把我们传入的组件作为子组件返回，并把合成的新的props传给我们编写的组件    
> 3、并在自身 componentDidMount 的时候，订阅数据更新    
```
// 组件初始化的时候订阅事件
componentDidMount() {
   ...
   this.subscription.trySubscribe()
   ...
}
// 函数内容，注意 onStateChange
trySubscribe() {
  if (!this.unsubscribe) {
    this.unsubscribe = this.parentSub
      ? this.parentSub.addNestedSub(this.onStateChange)
      : this.store.subscribe(this.onStateChange)

    this.listeners = createListenerCollection()
  }
}
```

> 4、componentWillUnmount 的时候销毁订阅事件   
> 5、变更触发逻辑
```
onStateChange() {
  ...
  if (!this.selector.shouldComponentUpdate) {
    ...
  } else {
    ...
    this.setState(dummyState) // dummyState = {}, 仅仅是为了触发更新
  }
}
```
