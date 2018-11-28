---
title: react基础
categories: "react"
tags:
  - JavaScript
  - react
---
### react基础
```
1.npm install -g create-react-app
2.create-react-app 项目名称
```
#### redux
```
    管理数据源和所有状态，负责展示的组件都可以采用无状态函数式的写法
```
#### 组件
##### 类别
有状态，无状态（也叫函数式组件），
```
有状态（state）:相同输入相同，输出结果可能不同。
```
```
无状态组件：无生命周期，只负责根据传入的props来展示，不涉及到要state状态的操作，相同输入，必定输出相同
***何时使用：
1.无需state,即不处理用户输入，组件所有数据都依赖props传入
2.不需要用到生命周期函数
***好处：
1.不需要生命类，避免大量的extends或constuctor这样的代码，简洁
2.不需要显示声明this,函数式声明的好处不需要像es6那样声明中将this绑定到当前作用域

```
##### 写法3种：
```
1.早期es5的写法 react.createClass({...})
2.es6写法（有状态组件用比较好）: react.Component
export default class Home extends Component {
  constructor(props) {
    super(props);
    this.state = {
      age: props.age,
      status: 0
    }
    setTimeout(() => {
      this.setState({
        status: 1
      })
    }, 3000)
  }
  onMakeOlder() {
    this.age += 3;
    this.setState({
      age: this.state.age + 3
    })
  }......
3.无状态写法
import React from 'react';
const Header = (pros) => {
    return (
      <div className="container">
        <div className="row">
          <div className="col-xs-1 col-xs-offset-11">
            <h1>Header</h1>
          </div>
          </div>
      </div>
    )
};
export default Header;
```

##### 用法
1 .闭合
2.不闭合(父组件可以传内容过去)
```
    <Home name={"max"} age={12} user={user}/>
    <Home name={"max"} age={12} user={user}>
      <p>I am child</p>
    </Home>
```
### 动态输出数据
```
 表达式： {}只能在一行
```

### 组件间数据传递
定义类型：
```
import React, { Component } from 'react';
import PropTypes from 'prop-types';
export default class Home extends Component {
  render() {
    return (
      <div className="container">
        <div className="row">
          <div className="col-xs-1 col-xs-offset-11">
            <h1> Home, name is { this.props.name}, age is {this.props.age }</h1>
            <ul>
              {this.props.user.hobbies.map((habby,index) => <li key={index}>{habby}</li>)}
            </ul>
          </div>
        </div>
        <div>{this.props.children}</div>
      </div>
    );
  }
}
Home.propTypes = {
  name: PropTypes.string,
  age: PropTypes.number,
  user: PropTypes.object
};
```
##### 1.父传子：
传值
```
    <Home name={"max"} age={12} user={user}/>
    <Home name={"max"} age={12} user={user}>
      <p>I am child</p>
    </Home>
```
传方法
1. 父组件定义方法
```
父：class App extends Component {
  onGreet() {
    alert("hello!");
  }
  render() {
    const user
    .....
```
2.
```
父：<Home name={"max"} age={12} user={user} greet={this.onGreet}/>
```
3.
```
子： <button className="btn btn-primary" onClick={this.props.greet}></button>
```
3.
```
1.兄弟或无关组件： eventEmitter(npm 包)
2.发布订阅模式
```
##### 2.子传父：
.....
  ##### 3.子传子
  过程：子 => 父 => 子
  首先父组件定义一个方法来改变当前的state，然后把事件传给一个子组件，当这个子组件触发这个事件并传值改变父组件的state，然后另一个子组件拿的那个父组件的值也相应更改
  #### 虚拟DOM（体现性能高）
  >改变该改变的地方，不用重新渲染

  >每操作一遍都会生成一个虚拟的DOM和之前的作比较（diff运算，找到改变的地方重新渲染）
  #### 看怎么改变局部
  f12 =>...=> more tools => rending => paint flashing打钩
## 事件
```
export default class Home extends Component {
  constructor(props) {
    super(props);
    this.state = {
      age: props.age
    }
    this.age = this.props.age;
  }
  onMakeOlder() {
    this.age += 3;
    this.setState({
      age: this.state.age + 3
    })
  }
  render() {
    return (
      <div className="container">
        <div className="row">
          <div className="col-xs-1 col-xs-offset-11">
            <h1> Home, name is { this.props.name}, age is {this.state.age }</h1>
            {/* <ul>
              {this.props.user.hobbies.map((habby,index) => <li key={index}>{habby}</li>)}
            </ul> */}
            <button onClick={this.onMakeOlder.bind(this)} className="btn btn-primary">Make me older</button>
          </div>
        </div>
        <div>{this.props.children}</div>
      </div>
    );
  }
  ```
  ### 组件的生命周期
  1.Mounting(被挂载时)
  ```
componentWillMount 在渲染前调用,在客户端也在服务端。
componentDidMount : 在第一次渲染后调用，只在客户端。之后组件已经生成了对应的DOM结构，可以通过this.getDOMNode()来进行访问。 如果你想和其他JavaScript框架一起使用，可以在这个方法中调用setTimeout, setInterval或者发送AJAX请求等操作(防止异部操作阻塞UI)。
```
  2.Updating(更新时)
  ```
componentWillReceiveProps 在组件接收到一个新的 prop (更新后)时被调用。这个方法在初始化render时不会被调用。
shouldComponentUpdate（可以发送请求） 返回一个布尔值。在组件接收到新的props或者state时被调用。在初始化时或者使用forceUpdate时不被调用。
可以在你确认不需要更新组件时使用。
componentWillUpdate在组件接收到新的props或者state但还没有render时被调用。在初始化时不会被调用。
componentDidUpdate 在组件完成更新后立即调用。在初始化时不会被调用。
```
  3.Unmounting(移除时)
  ```
  componentWillUnmount在组件从 DOM 中移除的时候立刻被调用。
  ```
