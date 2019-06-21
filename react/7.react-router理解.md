#### react-router

##### 作用

它通过管理 URL，实现组件的切换和状态的变化，开发复杂的应用几乎肯定会用到

##### 原理

1、使用了一个叫 history 的一个独立的第三方js库，可以用来兼容在不同浏览器、不同环境下对历史记录的管理，拥有统一的API。具体来说里面的history分为三类:      
HashHistory:  利用HTML5里面的history    
BrowserHistory :利用HTML5里面的history   
MemoryHistory : 在内存中进行历史记录的存储     

2、执行路由推进：      
```
createBrowserHistory: pushState、replaceState、popstate

createHashHistory: location.hash=*** location.replace() 、hashchange
在 react-router 内部注册了 window.addEventListener('hashchange', listener, false) 事件监听器

createMemoryHistory: 在内存中进行历史记录的存储
```

3、state的存储       
为了维护state的状态，将其存储在sessionStorage里面:

4、关于组件和API实现：

1）、Link
2)、Switch
> 渲染第一个被location匹配到的并且作为子元素的<Route>或者<Redirect>

<Switch>是唯一的因为它仅仅只会渲染一个路径。相比之下（不使用<Switch>包裹的情况下），每一个被location匹配到的<Route>将都会被渲染。认真思考一下图1-1的代码：

详情：https://www.jianshu.com/p/27ee7df4ccc1



##### OnEnter 和 Leave
用于进入页面验证、离开页面确认


##### 2/3 和 4的区别
React Router 4 与之前的版本最大的不同便是 router 在项目中的位置：

v2/v3 的版本采用的方式是将路由看成是一个整体的单元，与别的组件是分离的，一般会单独放到一个 router 文件中，对其进行集中式管理；并且，布局和页面的嵌套由路由的嵌套所决定。

```
import { Router, Route, IndexRoute, browserHistory } from 'react-router'

const PrimaryLayout = props => (
  <div className="primary-layout">
    <header>
      Our React Router 3 App
    </header>
    <main>
      {props.children}
    </main>
  </div>
)

const HomePage =() => <div>Home Page</div>
const UsersPage = () => <div>Users Page</div>

// 集中的 router，也可将其单独放到一个 router 文件中
const App = () => (
  <Router history={browserHistory}>
    <Route path="/" component={PrimaryLayout}>
      <IndexRoute component={HomePage} />
      <Route path="/users" component={UsersPage} />
    </Route>
  </Router>
)

render(<App />, document.getElementById('root'))
```

v4 的版本则将路由进行了拆分，将其放到了各自的模块中，不再有单独的 router 模块，充分体现了组件化的思想；另外，<BrowserRouter> 的使用与之前作为 history 属性传入的方式也不同了。

```
// v4 中改从 'react-router-dom' 引入的原因是因为还有个 native 版本，这个意味着是 web 版本
import { BrowserRouter, Route } from 'react-router-dom'

const PrimaryLayout = () => (
  <div className="primary-layout">
    <header>
      Our React Router 4 App
    </header>
    <main>
      <Route path="/" exact component={HomePage} />
      <Route path="/users" component={UsersPage} />
    </main>
  </div>
)

const HomePage =() => <div>Home Page</div>
const UsersPage = () => <div>Users Page</div>

const App = () => (
  <BrowserRouter>
    <PrimaryLayout />
  </BrowserRouter>
)

render(<App />, document.getElementById('root'))
```

##### 二、包含式路由与exact

精确匹配

##### 三、独立路由：Switch

##### 新增了很多api

react-router: 2、3和4的区别：https://www.jianshu.com/p/bf6b45ce5bcc

简约教程：http://react-china.org/t/react-router4/15843
