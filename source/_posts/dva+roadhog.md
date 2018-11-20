---
title: dva+roadhog
categories: "dva+roadhog"
tags:
  - dva+roadhog
  - react
---

#### 全局装个插件看dist文件夹里的内容（https://github.com/zeit/serve）
然后进到dist文件夹运行serve命令就可以看到

#### 配置多页面入口文件（https://github.com/sorrycc/roadhog） entry

1.src下新建个counter.js
```
import React from 'react';
import ReactDom from 'react-dom';
const Counter = (props) => {
    return (
        <div>hi.counter</div>
    )
}
ReactDom.render(<Counter />,document.getElementById('counter'))
```
2.在webpackrc里
```
{
    "entry": {
        "index": "./src/index.js",
        "counter": "./src/counter.js"
    }
}
```
3.在public新建counter.html文件
然后在浏览器（http://localhost:8000/counter.html）就可以看到效果

#### 两种使用css的方式，
> example.css3
```
p{
    color: red
}
.yellow{
    color: yellow
}
```
> example.js

```
import React from 'react';
import './Example.css'
import styles from './Example.css'
const Example = () => {
  return (
    <div>
      <p>Example</p>
      <h3 className={styles.yellow}>黄色</h3>
    </div>
  );
};
Example.propTypes = {
};
export default Example;
```
#### 使用less和scss
1.less默认安装了直接像css那样使用
2.scss文档说明了要安装（https://github.com/sorrycc/roadhog）
> sass
Configure the options for [node-sass] (https://github.com/sass/node-sass#options). Note: node-sass and sass-loader dependencies must be installed in the project directory when using sass.
npm install --save dev node-sass sass-loader
安装node-sass可能比较慢

#### 代理
> 处理请求的
首先跨域是和浏览器有关的（端口号，协议，或主机等不同），只是被浏览器阻止了，其他工具和程序访问可以访问，
通过webpack程序当中间层充当代理
** 代理即避免浏览器直接访问远程 **
浏览器 访问 <=> webpack-dev-server 去代理访问 <=> 远程
先安装axios之类的插件
1.在在webpackrc里
```
{
    "entry": {
        "index": "./src/index.js",
        "counter": "./src/counter.js"
    },
    "proxy": {
        "/api": {
            "target": "http://jsonplaceholder.typicode.com/",
            "changeOrigin": true,
            "pathRewrite": { "^/api" : "" }
        }
    }
}
```
2.在文件中就可使用
```
import React from 'react';
import axios from 'axios'
// import styles from './Example.css'
// import './Example.css'
import './Example.less'
const Example = () => {
  return (
    <div>
      <p>Example</p>
      <h3>黄色</h3>
      <button onClick={() => {axios.get('http://jsonplaceholder.typicode.com/users')}}>普通请求</button>
      <button onClick={() => {axios.get('/api/users')}}>请求代理</button>
    </div>
  );
};

Example.propTypes = {
};

export default Example;
```
