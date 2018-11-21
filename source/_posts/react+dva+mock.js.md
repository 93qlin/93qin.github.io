---
title: react+dva+mock
categories: "react+dva+mock"
tags:
  - react
  - mock.js
---
### mock.js
1.在.roadhogrc.mock.js文件下（https://github.com/sorrycc/roadhog）roadhog使用文档
```
export default {
    // Support type as Object and Array
    'GET /api/users': { users: [1,2] },

    // Method like GET or POST can be omitted
    '/api/users/1': { id: 1 },

    // Support for custom functions, the API is the same as express@4
    'POST /api/users/create': (req, res) => { res.end('OK'); },
  };
  ```
  2.就可使用
  ```
  import React from 'react';
import { connect } from 'dva';
import styles from './IndexPage.css';
import axios from 'axios'
function IndexPage() {
  return (
    <div className={styles.normal}>
    <button onClick={() => axios.get('/api/users')}>click me</button>
      <h1 className={styles.title}>Yay! Welcome to dva!</h1>
      <div className={styles.welcome} />
      <ul className={styles.list}>
        <li>To get started, edit <code>src/index.js</code> and save to reload.</li>
        <li><a href="https://github.com/dvajs/dva-docs/blob/master/v1/en-us/getting-started.md">Getting Started</a></li>
      </ul>
    </div>
  );
}

IndexPage.propTypes = {
};

export default connect()(IndexPage);
```

#### 优化,在mock文件夹下统一维护
1.mock文件夹下新建users.js等
```
module.exports = {
    'GET /api/users': { users: [1,2] },

}
```
2..roadhogrc.mock.js文件改为
```
export default {
   ...require('./mock/users'),
   ...require('./mock/posts'),
  };
```
就可以了
** 另一种roadhogrc.mock.js文件处理方式 **
```
import fs from 'fs';
import path from 'path';

const mock = {}
fs.readdirSync(path.join(__dirname + '/mock')).forEach(function (file) {
  if (file.match(/\.js$/)) {
    Object.assign(mock, require('./mock/' + file))
  }
})
export default mock
```
可以接回调函数
```
module.exports = {
    // 'GET /api/users': { users: [1,2] },
    'GET /api/users': (req, res) => {res.status(200).json({users: [1,2,3]}) },

}
```
#### 返回一个json
user.json
```
[
    {"a": 11},
    {"b":1}
]
```
然后:
```
// const data = [
//     {a: 1},
//     {b:1}
// ]
const data = require('./user.json')
module.exports = {
    // 'GET /api/users': { users: [1,2] },
    // 'GET /api/users': (req, res) => {res.status(200).json({users: [1,2,3]}) },
    'GET /api/users': (req, res) => {res.status(200).json({users: data}) },

}
```
#### 生成随机数据（https://github.com/nuysoft/Mock/wiki/Getting-Started）
```
const Mock = require('mockjs')
module.exports = {
    // 'GET /api/users': { users: [1,2] },
    'GET /api/users': (req, res) => {res.status(200).json({users: Mock.mock({
        // 属性 list 的值是一个数组，其中含有 1 到 10 个元素
        'list|1-10': [{
            // 属性 id 是一个自增数，起始值为 1，每次增 1
            'id|+1': 1
        }]
        })
    })
    },

}
```
得到：
```
{"users":{"list":[{"id":1},{"id":2},{"id":3},{"id":4},{"id":5},{"id":6},{"id":7},{"id":8},{"id":9},{"id":10}]}}
```
#### 数据模板定义(https://github.com/nuysoft/Mock/wiki/Syntax-Specification)(再对照官网)
> // 属性名   name
// 生成规则 rule
// 属性值   value
'name|rule': value

#### 数据占位符（https://github.com/nuysoft/Mock/wiki/Mock.Random）
```
var Random = Mock.Random
Random.email()
// => "n.clark@miller.io"
Mock.mock('@email')
// => "y.lee@lewis.org"
Mock.mock( { email: '@email' } )
// => { email: "v.lewis@hall.gov" }
```

```
const Mock = require('mockjs')
module.exports = {
    // 'GET /api/users': { users: [1,2] },
    'GET /api/users': (req, res) => {res.status(200).json({users: Mock.mock({
        'email|10': [
            '@email'
        ]
        })
    })
    },

}
```
得到：
```
  {"users":{"email":["h.stwgfmvr@pbwcf.bs","i.njzexjrvwn@onrwqm.as","k.cslhnnq@vpreuhdvxc.tv","u.owivlgrc@bji.ar","t.pulpiwanp@ubh.aq","f.qcea@cxwgvt.vn","d.hcmz@iihjjr.ee","i.xsqtdssbw@cmvfjqmf.fm","c.jleblu@uidou.hr","q.hobb@dks.ye"]}}
  ```
  #### 最后去别人的项目看别人怎么玩的
  https://github.com/bowencool/dva-antd-admin/blob/master/mock/list.js

https://github.com/LANIF-UI/dva-boot-admin/blob/master/src/utils/packMock.js

https://github.com/Mesamo/dva-admin/blob/master/.roadhogrc.mock.js

https://github.com/bj75326/B-A/blob/master/mock/notices.js

https://github.com/zuiidea/antd-admin/blob/master/mock/user.js

https://github.com/liuxx001/bird-front/blob/master/mock/common.js
