# React03

## 1、路由

根据不同的路径，显示不同的组件内容

React使用的库

```sh
cnpm install react-router-dom
```

index.js

```js
import React from 'react';
import ReactDOM from 'react-dom';
// 导入App.js
import App from './App'
ReactDOM.render(
  <App />
  , document.querySelector("#root")
)
```

App.js

```js
// 导入 react  如果不导入 无法使用jsx
import React from 'react';
// hash模式
// import {HashRouter as Router,Link,Route} from 'react-router-dom'
// history模式
import {BrowserRouter as Router,Link,Route} from 'react-router-dom'
function App() {
  return (
    <div className="App">
      <h1>首页</h1>
    </div>
  );
}

export default App;

```

hash(#)路由 #后面的变更不会触发我们的http请求

history后端配合使用，用户以为就是普通页面，实际是但也年应用

#### 定义三个组件当页面

```js
// 导入 react  如果不导入 无法使用jsx
import React from 'react';
// hash模式
// import {HashRouter as Router,Link,Route} from 'react-router-dom'
// history模式
import { BrowserRouter as Router, Link, Route } from 'react-router-dom'

function Home() {
  return (
    <div>
      <h1>首页App</h1>
    </div>
  )
}
function Me() {
  return (
    <div>
      <h1>我的</h1>
    </div>
  )
}
function News() {
  return (
    <div>
      <h1>新闻</h1>
    </div>
  )
}

class App extends React.Component{
  render(){
    return (
      <div id="app">
        <h1>路由</h1>
      </div>
    )
  }
}

export default App;

```

### ReactRouter三大组件

Router：所有路由组件的根组件，包裹路由规则的最外层增容器

Route：路由规则匹配组件，显示当前规则对应的组件

LInk：路由跳转的位置，对应Route里的path

```js
// 导入 react  如果不导入 无法使用jsx
import React from 'react';
// hash模式
// import {HashRouter as Router,Link,Route} from 'react-router-dom'
// history模式
import { BrowserRouter as Router, Link, Route } from 'react-router-dom'

function Home() {
  return (
    <div>
      <h1>首页App</h1>
    </div>
  )
}
function Me() {
  return (
    <div>
      <h1>我的</h1>
    </div>
  )
}
function News() {
  return (
    <div>
      <h1>新闻</h1>
    </div>
  )
}

class App extends React.Component{
  render(){
    return (
      <div id="app">
        <h1>路由</h1>
        <Router>
          <div>
            <Link to="/">首页</Link>
            <Link to="/me">我的</Link>
            <Link to="/news">新闻</Link>
          </div>
          <Route path="/" component={Home}></Route>
          <Route path="/me" component={Me}></Route>
          <Route path="/news" component={News}></Route>
        </Router>
      </div>
    )
  }
}

export default App;

```

发现每页都有 / 下的组件内容 因为是模糊匹配

```js
/
/news
/me
都先访问的/
```

如果要精确匹配，那么可以在route上设置exact属性

```js
 <Route path="/" exact component={Home}></Route>
```

### 设置基础路径

```js
<Router basename="/admin">
          <div>
            <Link to="/">首页</Link>
            <Link to="/me">我的</Link>
            <Link to="/news">新闻</Link>
          </div>
          <Route path="/" exact component={Home}></Route>
          <Route path="/me" component={Me}></Route>
          <Route path="/news" component={News}></Route>
        </Router>
```

设置为/admin 之后访问 路径前都会加上admin

### 按路径细粒度的控制路由

```js
// 导入 react  如果不导入 无法使用jsx
import React from 'react';
// hash模式
// import {HashRouter as Router,Link,Route} from 'react-router-dom'
// history模式
import { BrowserRouter as Router, Link, Route } from 'react-router-dom'

function Home() {
  return (
    <div>
      <h1>首页App</h1>
    </div>
  )
}
function Me() {
  return (
    <div>
      <h1>我的</h1>
    </div>
  )
}
function News() {
  return (
    <div>
      <h1>新闻</h1>
    </div>
  )
}

class App extends React.Component{
  render(){
    return (
      <div id="app">
        <h1>路由</h1>
        <Router >
          <div>
            <Link to="/">首页</Link>
            <Link to="/me">我的</Link>
            <Link to="/news">新闻</Link>
          </div>
          <Route path="/" exact component={Home}></Route>
          <Route path="/me" component={Me}></Route>
          <Route path="/news" component={News}></Route>
        </Router>
        <Router basename="/admin">
          <div>
            <Link to="/">admin首页</Link>
            <Link to="/me">admin我的</Link>
            <Link to="/news">admin新闻</Link>
          </div>
          <Route path="/" exact component={Home}></Route>
          <Route path="/me" component={Me}></Route>
          <Route path="/news" component={News}></Route>
        </Router>
      </div>
    )
  }
}

export default App;

```

### 组件形式

```js
// 导入 react  如果不导入 无法使用jsx
import React from 'react';
// hash模式
// import {HashRouter as Router,Link,Route} from 'react-router-dom'
// history模式
import { BrowserRouter as Router, Link, Route } from 'react-router-dom'

function Home() {
  return (
    <div>
      <h1>首页App</h1>
    </div>
  )
}
function Me() {
  return (
    <div>
      <h1>我的</h1>
    </div>
  )
}
function News() {
  return (
    <div>
      <h1>新闻</h1>
    </div>
  )
}

function Product(){
  return (
    <Router basename="/admin">
      <div>
        <Link to="/">admin首页</Link>
        <Link to="/me">admin我的</Link>
        <Link to="/news">admin新闻</Link>
      </div>
      <Route path="/" exact component={Home}></Route>
      <Route path="/me" component={Me}></Route>
      <Route path="/news" component={News}></Route>
    </Router>
  )
}

class App extends React.Component {
  render() {
    return (
      <div id="app">
        <h1>路由</h1>
        <Router >
          <div>
            <Link to="/">首页</Link>
            <Link to="/me">我的</Link>
            <Link to="/news">新闻</Link>
          </div>
          <Route path="/" exact component={Home}></Route>
          <Route path="/me" component={Me}></Route>
          <Route path="/news" component={News}></Route>
        </Router>
        <Product></Product>
      </div>
    )
  }
}

export default App;

```

### 路由传参

```js
// 导入 react  如果不导入 无法使用jsx
import React from 'react';
// hash模式
// import {HashRouter as Router,Link,Route} from 'react-router-dom'
// history模式
import { BrowserRouter as Router, Link, Route } from 'react-router-dom'

function Home() {
  return (
    <div>
      <h1>首页App</h1>
    </div>
  )
}
function Me() {
  return (
    <div>
      <h1>我的</h1>
    </div>
  )
}
function News() {
  return (
    <div>
      <h1>新闻</h1>
    </div>
  )
}

function Product(){
  return (
    <Router basename="/admin">
      <div>
        <Link to="/">admin首页</Link>
        <Link to="/me">admin我的</Link>
        <Link to="/news">admin新闻</Link>
      </div>
      <Route path="/" exact component={Home}></Route>
      <Route path="/me" component={Me}></Route>
      <Route path="/news" component={News}></Route>
    </Router>
  )
}

class App extends React.Component {
  render() {
    let urlParams = { pathname:'/me',search:'?username=tom',hash:'#abc',state:{msg:'hello'}}
    return (
      <div id="app">
        <h1>路由</h1>
        <Router >
          <div>
            <Link to="/">首页</Link>
            <Link to={urlParams}>我的</Link>
            <Link to="/news">新闻</Link>
          </div>
          <Route path="/" exact component={Home}></Route>
          <Route path="/me" component={Me}></Route>
          <Route path="/news" component={News}></Route>
        </Router>
        <h1>Product 组件路由</h1>
        <Product></Product>
      </div>
    )
  }
}

export default App;

```

### 获取参数

```js
function Me(props) {
  console.log(props.location.state);
  return (
    <div>
      <h1>我的{props.location.state.msg}</h1>
    </div>
  )
}
```

### 动态路由

```js
<Router basename="/admin">
      <div>
        <Link to="/">admin首页</Link>
        <Link to="/me">admin我的</Link>
        <Link to="/news/110">admin新闻</Link>
      </div>
      <Route path="/" exact component={Home}></Route>
      <Route path="/me" component={Me}></Route>
      <Route path="/news/:id" component={News}></Route>
    </Router>
```

```js
function News(props) {
  console.log(props);
  return (
    <div>
      <h1>新闻id:{props.match.params.id}</h1>
    </div>
  )
}
```

## 2、重定向

如果访问某个页面使用的是重定向，那么就会就该页面路径

比如登陆成功跳转到其他页面

```js
// 导入 react  如果不导入 无法使用jsx
import React from 'react';
// 重定向
import { BrowserRouter as Router, Link, Route, Redirect } from 'react-router-dom'

function Login(props) {
  console.log(props.match.params.state);
  var s = props.match.params.state
  if (s === "ok") {
    // 重定向
    return <Redirect to="/admin"></Redirect>
  } else {
    return <Redirect to="/"></Redirect>
  }

}

function Home() {
  return (
    <div>
      <h1>Home</h1>
    </div>
  )
}
function Admin() {
  return (
    <div>
      <h1>欢迎！！！Admin</h1>
    </div>
  )
}
class App extends React.Component {
  render() {
    return (
      <div id="app">
        <Router >
          <div>
            <Link to="/">首页</Link>
            <Link to="/loginOK/no">登陆</Link>
          </div>
          <Route path="/" exact component={Home}></Route>
          <Route path="/loginOK/:state" component={Login}></Route>
          <Route path="/admin" component={Admin}></Route>
        </Router>
      </div>
    )
  }
}

export default App;

```

## 3、switch组件

路由匹配到第一个后还会想继续向下匹配，如果我有两个路由地址一样

```js
// 导入 react  如果不导入 无法使用jsx
import React from 'react';
// 重定向
import { BrowserRouter as Router, Link, Route, Redirect } from 'react-router-dom'

function Login(props) {
  console.log(props.match.params.state);
  var s = props.match.params.state
  if (s === "ok") {
    // 重定向
    return <Redirect to="/admin"></Redirect>
  } else {
    return <Redirect to="/"></Redirect>
  }

}

function Home() {
  return (
    <div>
      <h1>Home</h1>
    </div>
  )
}
function Admin() {
  return (
    <div>
      <h1>欢迎！！！Admin</h1>
    </div>
  )
}
function Admin1() {
  return (
    <div>
      <h1>欢迎！！！Admin111111</h1>
    </div>
  )
}
class App extends React.Component {
  render() {
    return (
      <div id="app">
        <Router >
          <div>
            <Link to="/">首页</Link>
            <Link to="/loginOK/ok">登陆</Link>
          </div>
          <Route path="/" exact component={Home}></Route>
          <Route path="/loginOK/:state" component={Login}></Route>
          <Route path="/admin" component={Admin}></Route>
          <Route path="/admin" component={Admin1}></Route>
        </Router>
      </div>
    )
  }
}

export default App;

```

使用switch包裹，后面的路由将不会匹配

```js
import { BrowserRouter as Router, Link, Route, Redirect,switch } from 'react-router-dom'


class App extends React.Component {
  render() {
    return (
      <div id="app">
        <Router >
          <div>
            <Link to="/">首页</Link>
            <Link to="/loginOK/ok">登陆</Link>
          </div>
          <Switch>
            <Route path="/" exact component={Home}></Route>
            <Route path="/loginOK/:state" component={Login}></Route>
            <Route path="/admin" component={Admin}></Route>
            <Route path="/admin" component={Admin1}></Route>
          </Switch>
        </Router>
      </div>
    )
  }
}
```

## 4、js跳转页面

```js
class App extends React.Component {
  render() {
    return (
      <div id="app">
        <Router >
          <div>
            <Link to="/">首页</Link>
            <Link to="/loginOK/ok">登陆</Link>
          </div>
          <Switch>
            <Route path="/" exact component={Home}></Route>
            <Route path="/loginOK/:state" component={Login}></Route>
            <Route path="/admin" component={Admin}></Route>
            <Route path="/admin" component={Admin1}></Route>
            <Route path="/child" component={Go}></Route>
          </Switch>
        </Router>
      </div>
    )
  }
}
```

```js
class Go extends React.Component {
  render() {
    return (
       <div>
         <button onClick={this.goto}>跳转页面</button>
       </div>
    )
  }
  goto=()=>{
    console.log(this.props);
    this.props.history.push("/")
  }
}
```

```js
//传数据
this.props.history.push("/",{msg:"hello"})
```

## 5、AntUI 

PC端

https://ant.design/index-cn

移动端

https://mobile.ant.design/index-cn

安装

```js
cnpm install antd-mobile --save
```

index.js

```js
import React from 'react';
import ReactDOM from 'react-dom';
import { Button } from 'antd-mobile';
ReactDOM.render(<Button>Start</Button>, document.querySelector("#root"));
//没有样式
```

```js
import 'antd-mobile/dist/antd-mobile.css';
```

以按钮为例：

```js
import React from 'react';
import ReactDOM from 'react-dom';
import { Button, WhiteSpace, WingBlank } from 'antd-mobile';
import 'antd-mobile/dist/antd-mobile.css';

const ButtonExample = () => (
  <WingBlank>
    <Button>default</Button><WhiteSpace />
    <Button disabled>default disabled</Button><WhiteSpace />

    <Button type="primary">primary</Button><WhiteSpace />
    <Button type="primary" disabled>primary disabled</Button><WhiteSpace />

    <Button type="warning">warning</Button><WhiteSpace />
    <Button type="warning" disabled>warning disabled</Button><WhiteSpace />

    <Button loading>loading button</Button><WhiteSpace />
    <Button icon="check-circle-o">with icon</Button><WhiteSpace />
    <Button icon={<img src="https://gw.alipayobjects.com/zos/rmsportal/jBfVSpDwPbitsABtDDlB.svg" alt="" />}>with custom icon</Button><WhiteSpace />
    <Button icon="check-circle-o" inline size="small" style={{ marginRight: '4px' }}>with icon and inline</Button>
    <Button icon="check-circle-o" inline size="small">with icon and inline</Button>
    <WhiteSpace />

    {/* <Button activeStyle={false}>无点击反馈</Button><WhiteSpace /> */}
    {/* <Button activeStyle={{ backgroundColor: 'red' }}>custom feedback style</Button><WhiteSpace /> */}

    <WhiteSpace />
    <Button type="primary" inline style={{ marginRight: '4px' }}>inline primary</Button>
    {/* use `am-button-borderfix`. because Multiple buttons inline arranged, the last one border-right may not display */}
    <Button type="ghost" inline style={{ marginRight: '4px' }} className="am-button-borderfix">inline ghost</Button>

    <WhiteSpace />
    <Button type="primary" inline size="small" style={{ marginRight: '4px' }}>primary</Button>
    <Button type="primary" inline size="small" disabled>primary disabled</Button>
    <WhiteSpace />
    <Button type="ghost" inline size="small" style={{ marginRight: '4px' }}>ghost</Button>
    {/* use `am-button-borderfix`. because Multiple buttons inline arranged, the last one border-right may not display */}
    <Button type="ghost" inline size="small" className="am-button-borderfix" disabled>ghost disabled</Button>
  </WingBlank>
);
ReactDOM.render(<ButtonExample />, document.querySelector("#root"));
```

安装axios

```js
cnpm install axios
```

测试数据地址

```js
https://api.github.com/users/Fire-zy/repos
```

```js
import React from 'react';
import ReactDOM from 'react-dom';
import { Button, WhiteSpace, WingBlank } from 'antd-mobile';
import 'antd-mobile/dist/antd-mobile.css';
import axios from 'axios'

const ButtonExample = () => (
  <div>

    <Button type="primary" onClick={get}>primary</Button><WhiteSpace />
  </div>
);
async function get() {
  let { data } = await axios.get('https://api.github.com/users/Fire-zy/repos')
  console.log(data);
}
ReactDOM.render(<ButtonExample />, document.querySelector("#root"));
```

