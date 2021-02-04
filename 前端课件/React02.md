# React02

## 1、事件

特点

- 事件名称 驼峰命名
- {} 传入一个函数，而不是字符串

### 事件对象

```js
import React from 'react';
import ReactDOM from 'react-dom';
class Clock extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      msg: "子组件的msg"
    }
  }
  render() {
    console.log(this);
    return (
      <div>
        <button onClick={this.send}>传递msg给父组件</button>
      </div>
    )
  }
  send = ()=>{
    console.log(this.state.msg);
    console.log(this.props);
    this.props.setChildData(this.state.msg)
  }
}
class Hello extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      childData: "haha",
      age: 18
    }
    // this.setChildData = this.setChildData.bind(this)
  }
  render() {
    return (
      <div >
        <h1 >{this.state.age} </h1>
        <h1 >子传递过来的数据：{this.state.childData} </h1>
        {/* 1 先把函数传给子组件 */}
        <Clock setChildData={this.setChildData} />
        <button onClick={this.evet}>事件对象</button>
      </div>
    )
  }
  evet(e){
    console.log(e);
  }
  // ES6 写法 不用再上面写bind了
  setChildData = (data) => {
    console.log(data);
    this.setState({
      childData: data
    })
  }
}
ReactDOM.render(
  <Hello name="tom" />
  , document.querySelector("#root")
)
```

### 阻止默认行为

```js
 <a onClick={this.evet} href="http://www.baidu.com">阻止默认行为</a>



 evet(e){
    console.log(e);
    e.preventDefault();
  }
```

### 事件传参

```js
<button onClick={()=>{this.evet("hello")}}>事件传参</button> 
evet(msg){
    console.log(msg);
}


<button onClick={(e)=>{this.evet("hello",e)}}>事件传参</button>
evet(msg,e){
    console.log(msg);
    console.log(e);
}
```

## 2、循环列表

```js
import React from 'react';
import ReactDOM from 'react-dom';

class Welcome extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      list: [
        {
          id: '01',
          name: 'tom'
        },
        {
          id: '02',
          name: 'jerry'
        }
      ]
    }
  }
  render() {
    let arr = []
    for (let i = 0; i < this.state.list.length; i++) {
      let item = (
        <li>
          <p>{this.state.list[i].id}</p>
          <p>{this.state.list[i].name}</p>
        </li>
      )
      arr.push(item)
    }
    return (
      <div>
        <ul>
          {arr}
        </ul>
      </div>
    )
  }
}
ReactDOM.render(
  <Welcome />
  , document.querySelector("#root")
)
```

改造代码

```js
import React from 'react';
import ReactDOM from 'react-dom';

class Welcome extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      list: [
        {
          id: '01',
          name: 'tom'
        },
        {
          id: '02',
          name: 'jerry'
        }
      ]
    }
  }
  render() {
    let arr = this.state.list.map((item,index)=>{
      return (
        <li key={index}>
          <p>{item.id}</p>
          <p>{item.name}</p>
        </li>
      )
    })
    return (
      <div>
        <ul>
          {arr}
        </ul>
      </div>
    )
  }
}
ReactDOM.render(
  <Welcome />
  , document.querySelector("#root")
)
```

改造成组件

```js
import React from 'react';
import ReactDOM from 'react-dom';
function ListItem(props) {
  return (
    <li>
      <p>{props.index+1}</p>
      <p>{props.data.id}</p>
      <p>{props.data.name}</p>
    </li>
  )
}
class Welcome extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      list: [
        {
          id: '01',
          name: 'tom'
        },
        {
          id: '02',
          name: 'jerry'
        }
      ]
    }
  }
  render() {
    let arr = this.state.list.map((item, index) => {
      return (
        <ListItem key={index} data={item} index={index}></ListItem>
      )
    })
    return (
      <div>
        <ul>
          {arr}
        </ul>
      </div>
    )
  }
}
ReactDOM.render(
  <Welcome />
  , document.querySelector("#root")
)
```

有事件，动态交互

```js
import React from 'react';
import ReactDOM from 'react-dom';
// function ListItem(props) {
//   return (
//     <li>
//       <p>{props.index + 1}</p>
//       <p>{props.data.id}</p>
//       <p>{props.data.name}</p>
//     </li>
//   )
// }
class LT extends React.Component {
  constructor(props) {
    super(props)
  }
  render() {
    return (
      <li onClick={(e)=>{this.show(this.props.index,this.props.data.id,this.props.data.name,e)}}>
        <p>{this.props.index + 1}</p>
        <p>{this.props.data.id}</p>
        <p>{this.props.data.name}</p>
      </li>
    )
  }
  show = (index,id,name,e)=>{
    console.log(e);
    console.log(index,id,name);
  }
}

class Welcome extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      list: [
        {
          id: '01',
          name: 'tom'
        },
        {
          id: '02',
          name: 'jerry'
        }
      ]
    }
  }
  render() {
    let arr = this.state.list.map((item, index) => {
      return (
        <LT key={index} data={item} index={index}></LT>
      )
    })
    return (
      <div>
        <ul>
          {arr}
        </ul>
      </div>
    )
  }
}
ReactDOM.render(
  <Welcome />
  , document.querySelector("#root")
)
```

简化

```js
import React from 'react';
import ReactDOM from 'react-dom';

class Welcome extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      list: [
        {
          id: '01',
          name: 'tom'
        },
        {
          id: '02',
          name: 'jerry'
        }
      ]
    }
  }
  render() {

    return (
      <div>
        <ul>
          {
            this.state.list.map((item, index) => {
              return (
                <li key={index} onClick={(e) => { this.show(index, item.id, item.name, e) }}>
                  <p>{index + 1}</p>
                  <p>{item.id}</p>
                  <p>{item.name}</p>
                </li>
              )
            })
          }
        </ul>
      </div>
    )
  }
  show = (index, id, name, e) => {
    console.log(e);
    console.log(index, id, name);
  }
}
ReactDOM.render(
  <Welcome />
  , document.querySelector("#root")
)
```

## 4、生命周期

3个状态

- Mounting:将组件插入到DOM中
- Updating:将数据更新到DOM中
- Unmounting:将组件移除DOM

钩子函数

- CompontWillMount 组件将要渲染（ajax、添加动画前的类）
- CompontDidMount 组件渲染完毕
- CompontWillReceiveProps:组件将要接收props数据（查看接收props的数据是什么）
- ShouldCompontUpdate 在组件接收到新的状态或者新的数据，判断是否要更新，返回boolean值 true更新，false不更新
- CompontWillUpdate 组件将要更新
- CompontDidUpdate 组件更新完毕
- CompontWillUnmount 组件将要卸载

## 5、echarts

安装 react不支持5.0以上版本

```sh
cnpm install echarts@4.9.0
```

```js
import React from 'react';
import ReactDOM from 'react-dom';
import echarts from 'echarts'
import './App.css'
class Welcome extends React.Component {
  constructor(props) {
    super(props)
  }
  render() {
    return (
      <div>
        <h1>echarts</h1>
        <div id="main"></div>
      </div>
    )
  }
}

ReactDOM.render(
  <Welcome />
  , document.querySelector("#root")
)
```

```css
#main{
  width: 600px;
  height:400px;
  background-color: red;
}

```

初始化echarts

```js
import React from 'react';
import ReactDOM from 'react-dom';
import echarts from 'echarts'
import './App.css'
class Welcome extends React.Component {
  constructor(props) {
    super(props)
    this.state = {}
  }
  render() {
    return (
      <div>
        <h1>echarts</h1>
        <div id="main"></div>
      </div>
    )
  }
  componentDidMount() {
    var myChart = echarts.init(document.getElementById('main'));
    console.log(myChart);
    // 指定图表的配置项和数据
    var option = {
      title: {
        text: 'ECharts 入门示例'
      },
      tooltip: {},
      legend: {
        data: ['销量']
      },
      xAxis: {
        data: ["衬衫", "羊毛衫", "雪纺衫", "裤子", "高跟鞋", "袜子"]
      },
      yAxis: {},
      series: [{
        name: '销量',
        type: 'bar',
        data: [5, 20, 36, 10, 10, 20]
      }]
    };

    // 使用刚指定的配置项和数据显示图表。
    myChart.setOption(option);
  }
}

ReactDOM.render(
  <Welcome />
  , document.querySelector("#root")
)
```

## 6、redux

解决React数据管理（状态管理），用于中大型，数据比较庞大，组件数据共享，组件之间数据交互多的情况下使用

如果你不知道是否需要使用redux，那就不要使用它

- 解决组件的数据通信
- 解决数据和交互较多的应用

只是一种状态管理的解决方案

### Store：数据仓库，保存数据的地方

### State：是一个对象，数仓里的所有数据都放到1个state里

### Action：1个动作，可以触发数据改变的方法

### Dispatch: 将动作触发成方法

### reducer: 是一个函数，通过获取动作改变数据，生成新的状态，从而改变页面

安装

```js
cnpm install redux
```

index.js

导入redux

```js
import {createStore} from 'redux'
```

```js
import React from 'react';
import ReactDOM from 'react-dom';
// 导入redux ,创建仓库
import {createStore} from 'redux'

// 函数式计数器组件
const Count = function (props) {
  return (
    <div>
      <h1>计数数量：</h1>
      <button onClick={add}>增加</button>
      <button  onClick={sub}>减少</button>
    </div>
  )
}

function add(){

}
function sub(){

}


ReactDOM.render(
  <App />
  , document.querySelector("#root")
)
```

创建仓库

```js
import React from 'react';
import ReactDOM from 'react-dom';
// 导入redux ,创建仓库
import { createStore } from 'redux'
// state={num}函数默认值
// action 根据动作 返回什么状态
const reducer = function (state = { num: 0 }, action) {
  switch (action.type) {
    case 'add':
      state.num++
      break;
    case 'sub':
      state.num--
      break;
    default:

  }
  return state

}


const store = createStore(reducer)


// 函数式计数器组件
const Count = function (props) {
  return (
    <div>
      <h1>计数数量：</h1>
      <button onClick={add}>增加</button>
      <button onClick={sub}>减少</button>
    </div>
  )
}

function add() {

}
function sub() {

}


ReactDOM.render(
  <App />
  , document.querySelector("#root")
)
```

通过仓库方法dispatch调用方法

```js
import React from 'react';
import ReactDOM from 'react-dom';
// 导入redux ,创建仓库
import { createStore } from 'redux'
// state={num}函数默认值
// action 根据动作 返回什么状态
const reducer = function (state = { num: 0 }, action) {
  switch (action.type) {
    case 'add':
      state.num++
      break;
    case 'sub':
      state.num--
      break;
    default:

  }
   return state

}

const store = createStore(reducer)
console.log(store)

// 函数式计数器组件
const Count = function (props) {
  return (
    <div>
      <h1>计数数量：{store.getState().num}</h1>
      <button onClick={add}>增加</button>
      <button onClick={sub}>减少</button>
    </div>
  )
}

function add() {
  store.dispatch({type:"add"})
    console.log(store.getState());
}
function sub() {
  store.dispatch({type:"sub"})
}


ReactDOM.render(
  <App />
  , document.querySelector("#root")
)
```

页面渲染

```js
ReactDOM.render(
  <Count />
  , document.querySelector("#root")
)
```

此时测试发现 打印num是变化的，但是页面是不变的

监听内容，渲染函数 用 subscribe()

```js
import React from 'react';
import ReactDOM from 'react-dom';
// 导入redux ,创建仓库
import { createStore } from 'redux'
// state={num}函数默认值
// action 根据动作 返回什么状态
const reducer = function (state = { num: 0 }, action) {
  switch (action.type) {
    case 'add':
      state.num++
      break;
    case 'sub':
      state.num--
      break;
    default:

  }
  return state
}

const store = createStore(reducer)
console.log(store);
// 函数式计数器组件
const Count = function (props) {
  return (
    <div>
      <h1>计数数量：{store.getState().num}</h1>
      <button onClick={add}>增加</button>
      <button onClick={sub}>减少</button>
    </div>
  )
}

function add() {
  store.dispatch({ type: "add" })
  console.log(store.getState());
}
function sub() {
  store.dispatch({ type: "sub" })
}


ReactDOM.render(
  <Count />
  , document.querySelector("#root")
)

store.subscribe(() => {
  ReactDOM.render(
    <Count />
    , document.querySelector("#root")
  )
})
```

还可以传递数据

```js
function add() {
  store.dispatch({ type: "add" })
  // 传递数据
  // store.dispatch({ type: "add",content:"hello" })
  console.log(store.getState());
}

//打印action 可以看到数据
const reducer = function (state = { num: 0 }, action) {
  console.log(action);
  switch (action.type) {
    case 'add':
      state.num++
      break;
    case 'sub':
      state.num--
      break;
    default:

  }
  return state
}
```







