---
title: dva
categories: "dva"
tags:
  - react
  - es6
  - dva
---
### connect mapStateToProps两种写法
1.
// const mapStateToProps = (state) => {
//   return {
//       counter: state.counter
//   }
// }
2.
const mapStateToProps = ({ counter }) => ({
    counter,
  })

export default connect(mapStateToProps)(CounterPage)


#### 向子组件传值
方式1，可以将所有的props包含siapach,路由信息等传过去，也许子组件用到
```
const CounterPage  = (props) => {
    return(
        <div>
            <div>Counter</div>
            <Counter { ...props }/>
        </div>


    )
}
```
方式2
```
const CounterPage  = ({counter}) => {
    return(
        <div>
            <div>Counter</div>
            <Counter counter={counter}/>
        </div>


    )
}
```
##### 触发action dispath： 命名空间 + '/'+ 要触发的
子组件
```
const Counter = ({counter,dispatch}) => {
    // console.log(props.counter)
    return (
        <div>
            <h1>{counter.count}</h1>
            <button onClick={() => {dispatch({type: 'counter/add'})}}>+</button>   找到counte并触发add,不用维护常量
        </div>
    )
}
```
counter的add
```
reducers: {
    add(state, action) {
      return {  
        count: state.count + 1
      }
    }
  },
```
还可以这样写
```
<button onClick={() => {dispatch({type: 'counter/login/start'})}}>+</button>
```
项目复杂，有层次感
```
reducers: {
    'login/start'(state, action) {
      return {  
        count: state.count + 1
      }
    }
  },
```

#### 处理异步

```
<button onClick={() => {dispatch({type: 'counter/asyncAdd'})}}>+ async</button>
```
```
import {delay} from 'dva/saga'
effects: {
     const counter = yield select(state => state.counter)  // ** selset的作用 **取出state的值
     // const counter = yield select(state => state.counter)  
     //const {counter} = yield select(_ => _)
     // const counter = yield select(({counter}) => counter) // ** 四种写法 **
     yield call(delay,1000)
     yield put({ type: 'add' });
   },
 }
 ```
#### 统一处理model里的文件不用一个个写：
之前在index文件里
```
// 3. Model
app.model(require('./models/counter').default);
app.model(require('./models/example').default);
```
现在在model文件夹里新建index文件遍历其他文件利用webpack的** require.context **
```
const context = require.context('./',false,/\.js$/);
export default context
    .keys()
    .filter(item => item !== './index.js')
    .map(key => context(key))
```
```
require('./models').default.forEach(key => app.model(key.default))
```

#### 在dva里使用redux-action,解决多个文件dispatch的问题
npm install --save redux-actions  yarn add redux-actions

1.src下新建actions文件夹，新建index文件
```
import {createAction} from 'redux-actions'
export const counterAdd=createAction('counter/add')
export const counterAsyncAdd=createAction('counter/asyncAdd')
```

父组件
```
import React from 'react'
import Counter from '../components/Counter'
import {connect} from 'dva'
import PropTypes from 'prop-types'
** import {counterAdd, counterAsyncAdd} from '../actions' **
const CounterPage = ({counter, dispatch, counterAdd, counterAsyncAdd}) => {
    return (
        <div>
            <div>Counter</div>
            <Counter
                counterAdd={counterAdd}
                counterAsyncAdd={counterAsyncAdd}
                counter={counter}
                dispatch={dispatch}/>
        </div>

    )
}
Counter.prototypes = {
    counter: PropTypes.object
}
// const mapStateToProps = (state) => {   return {       counter: state.counter
//  } }

const mapStateToProps = ({counter}) => ({counter})

export default connect(mapStateToProps, {counterAdd, counterAsyncAdd})(CounterPage)
```
子组件
```
import React from 'react'
import {connect} from 'dva' // eslint-disable-line
import PropTypes from 'prop-types' // eslint-disable-line

const Counter = ({counter,dispatch, counterAdd, counterAsyncAdd}) => {
    // console.log(props.counter)
    console.log(counterAsyncAdd);

    return (
        <div>
            <h1>{counter.count}</h1>
            <button onClick={() => {dispatch({type: 'counter/add',name: 'action1'})}}>+</button>
            <button onClick={() => {dispatch({type: 'counter/asyncAdd'})}}>+ async</button>
            <p></p>
            <button onClick={() => {counterAdd()}}>+</button>
            <button onClick={() => {counterAsyncAdd()}}>+ async</button>
        </div>
    )
}

export default Counter
```
#### 路由跳转五种方式
1.一层层跳转
router.js
```
import React from 'react';
import { Router, Route, Switch } from 'dva/router';
import IndexPage from './routes/IndexPage';
import CounterPage from './routes/CounterPage';

function RouterConfig({ history }) {
  return (
    <Router history={history}>
      <Switch>
        <Route path="/" exact component={IndexPage} />
        <Route path="/counter" exact component={CounterPage} />
      </Switch>
    </Router>
  );
}

export default RouterConfig;
```
父组件
```
import React from 'react'
import Counter from '../components/Counter'
import {connect} from 'dva'
import PropTypes from 'prop-types'
import {counterAdd, counterAsyncAdd} from '../actions'
const CounterPage = ({counter, dispatch, counterAdd, counterAsyncAdd,history}) => {
    return (
        <div>
            <div>Counter</div>
            <Counter
                counterAdd={counterAdd}
                counterAsyncAdd={counterAsyncAdd}
                counter={counter}
                dispatch={dispatch}
                history = {history}/>
        </div>

    )
}
Counter.prototypes = {
    counter: PropTypes.object
}
// const mapStateToProps = (state) => {   return {       counter: state.counter
//  } }

const mapStateToProps = ({counter}) => ({counter})

export default connect(mapStateToProps, {counterAdd, counterAsyncAdd})(CounterPage)
```
子组件
```
import React from 'react'
import {connect} from 'dva' // eslint-disable-line
import PropTypes from 'prop-types' // eslint-disable-line

const Counter = ({counter,dispatch, counterAdd, counterAsyncAdd,history}) => {
    // console.log(props.counter)
    // console.log(counterAsyncAdd);

    return (
        <div>
            <h1>{counter.count}</h1>
            <button onClick={() => history.push('./')}>GO HOME</button>
            <button onClick={() => {dispatch({type: 'counter/asyncAdd'})}}>+ async</button>
            <button onClick={() => {dispatch({type: 'counter/asyncAdd'})}}>+ async</button>
            <p></p>
            <button onClick={() => {counterAdd()}}>+</button>
            <button onClick={() => {counterAsyncAdd()}}>+ async</button>
        </div>
    )
}

export default Counter
```
2.withRouter(解决不是父子关系，难以一层层传递history的问题)
> 传统：import {withRouter} from 'react-router-dom'
  在dva中：import {withRouter} from 'dva/router'

  在需要的组件中包裹一下
  ```
  import React from 'react'
import {connect} from 'dva' // eslint-disable-line
import PropTypes from 'prop-types' // eslint-disable-line
import {withRouter} from 'dva/router'
const Counter = ({counter,dispatch, counterAdd, counterAsyncAdd,history}) => {
    // console.log(props.counter)
    // console.log(counterAsyncAdd);

    return (
        <div>
            <h1>{counter.count}</h1>
            <button onClick={() => history.push('./')}>GO HOME</button>
            <button onClick={() => {dispatch({type: 'counter/asyncAdd'})}}>+ async</button>
            <button onClick={() => {dispatch({type: 'counter/asyncAdd'})}}>+ async</button>
            <p></p>
            <button onClick={() => {counterAdd()}}>+</button>
            <button onClick={() => {counterAsyncAdd()}}>+ async</button>
        </div>
    )
}

export default withRouter(Counter)
```
3.Link方式
```
import React from 'react'
import {connect} from 'dva' // eslint-disable-line
import PropTypes from 'prop-types' // eslint-disable-line
import {withRouter,Link} from 'dva/router'
const Counter = ({counter,dispatch, counterAdd, counterAsyncAdd,history}) => {
    // console.log(props.counter)
    // console.log(counterAsyncAdd);

    return (
        <div>
            <h1>{counter.count}</h1>
            <Link to='/'>home page</Link>
            <button onClick={() => history.push('./')}>GO HOME</button>
            <button onClick={() => {dispatch({type: 'counter/asyncAdd'})}}>+ async</button>
            <button onClick={() => {dispatch({type: 'counter/asyncAdd'})}}>+ async</button>
            <p></p>
            <button onClick={() => {counterAdd()}}>+</button>
            <button onClick={() => {counterAsyncAdd()}}>+ async</button>
        </div>
    )
}

export default withRouter(Counter)
```
四.上下文
```
import React from 'react'
import {connect} from 'dva' // eslint-disable-line
import PropTypes from 'prop-types' // eslint-disable-line
import {withRouter,Link} from 'dva/router'
const Counter = ({counter,dispatch, counterAdd, counterAsyncAdd,history},context) => {
    // console.log(props.counter)
    // console.log(counterAsyncAdd);

    return (
        <div>
            <h1>{counter.count}</h1>
            <Link to='/'>home page</Link>
            <button onClick={() => context.router.history.push('./')}>context go back</button>
            <button onClick={() => history.push('./')}>GO HOME</button>
            <button onClick={() => {dispatch({type: 'counter/add'})}}>+ async</button>
            <button onClick={() => {dispatch({type: 'counter/asyncAdd'})}}>+ async</button>
            <p></p>
            <button onClick={() => {counterAdd()}}>+</button>
            <button onClick={() => {counterAsyncAdd()}}>+ async</button>
        </div>
    )
}

Counter.contextType = {
    router: PropTypes
}
export default withRouter(Counter)
```
五.routerRedux dispatch
```
import React from 'react'
import {connect} from 'dva' // eslint-disable-line
import PropTypes from 'prop-types' // eslint-disable-line
import {withRouter,Link,routerRedux} from 'dva/router'
const Counter = ({counter,dispatch,history},context) => {
    // console.log(props.counter)
    // console.log(counterAsyncAdd);

    return (
        <div>
            <h1>{counter.count}</h1>
            <button onClick={() => dispatch(routerRedux.push('./'))}>routerRedux back</button>
            <Link to='/'>home page</Link>
            <button onClick={() => context.router.history.push('./')}>context go back</button>
            <button onClick={() => history.push('./')}>GO HOME</button>
            <button onClick={() => {dispatch({type: 'counter/add'})}}>add</button>
            <button onClick={() => {dispatch({type: 'counter/asyncAdd'})}}>+ async</button>
            <p></p>
        </div>
    )
}

Counter.contextType = {
    router: PropTypes.object
}
export default withRouter(Counter)
```
所以可以在异步中处理（effect）
```
import {delay} from 'dva/saga'
import {routerRedux} from 'dva/router'
export default {
  namespace: 'counter', //命名空间相当于scoped
  state: {  //可以定义很多类型 state: 1
    count: 2
  },
  subscriptions: {
    setup({ dispatch, history }) {  // eslint-disable-line
    },
  },
  effects: {
    *asyncAdd({ payload }, { call, put, select }) {  // eslint-disable-line
      //const counter = yield select(state => state.counter)
      //const counter = yield select(_ => _.counter)
      //const {counter} = yield select(_ => _)
      const counter = yield select(({counter}) => counter) // eslint-disable-line
      yield call(delay,1000)
      yield put({ type: 'add' });
      yield put(routerRedux.push('/'));
    },
  },
  reducers: {
    add(state, action) {
      return {  
        count: state.count + 1
      }
    },
    // 'login/start'(state, action) {
    //   return {  
    //     count: state.count + 1
    //   }
    // }
  },
};
```
也可以这样写
```
import {delay} from 'dva/saga'
import {routerRedux} from 'dva/router'
export default {

  namespace: 'counter', //命名空间相当于scoped

  state: {  //可以定义很多类型 state: 1
    count: 2
  },

  subscriptions: {
    setup({ dispatch, history }) {  // eslint-disable-line
    },
  },

  effects: {
    *asyncAdd({ payload }, { call, put, select }) {  // eslint-disable-line
      //const counter = yield select(state => state.counter)
      //const counter = yield select(_ => _.counter)
      //const {counter} = yield select(_ => _)
      const counter = yield select(({counter}) => counter) // eslint-disable-line
      yield call(delay,1000)
      yield put({ type: 'add' });
      yield put(routerRedux.push({
        pathname: '/',
        hash: 'hello'
      }));
    },
  },

  reducers: {
    add(state, action) {
      return {  
        count: state.count + 1
      }
    },
    // 'login/start'(state, action) {
    //   return {  
    //     count: state.count + 1
    //   }
    // }
  },

};
```
> http://localhost:8000/#hello
还可以这样写
先装个插件
npm install query-string
作用转换查询字符串等等
```
console.log(location.hash);
//=> '#token=bada55cafe'

const parsedHash = queryString.parse(location.hash);
console.log(parsedHash);
//=> {token: 'bada55cafe'}

parsed.foo = 'unicorn';
parsed.ilike = 'pizza';

const stringified = queryString.stringify(parsed);
//=> 'foo=unicorn&ilike=pizza'
```
```
import {delay} from 'dva/saga'
import {routerRedux} from 'dva/router'
import querySting from 'query-string'
export default {
  namespace: 'counter', //命名空间相当于scoped
  state: {  //可以定义很多类型 state: 1
    count: 2
  },
  subscriptions: {
    setup({ dispatch, history }) {  // eslint-disable-line
    },
  },
  effects: {
    *asyncAdd({ payload }, { call, put, select }) {  // eslint-disable-line
      //const counter = yield select(state => state.counter)
      //const counter = yield select(_ => _.counter)
      //const {counter} = yield select(_ => _)
      const counter = yield select(({counter}) => counter) // eslint-disable-line
      yield call(delay,1000)
      yield put({ type: 'add' });
      yield put(routerRedux.push({
        pathname: '/',
        search: querySting.stringify({
          from: '深圳'
        })
      }));
    },
  },
  reducers: {
    add(state, action) {
      return {  
        count: state.count + 1
      }
    }
};

```
得到
> http://localhost:8000/?from=深圳

#### 订阅 subscriptions
** （history,库正则匹配路径path-to-regexp(https://github.com/pillarjs/path-to-regexp), ） **
```
import {delay} from 'dva/saga'
import {routerRedux} from 'dva/router'
import querySting from 'query-string'
import pathToRegexp from 'path-to-regexp'
export default {

  namespace: 'counter', //命名空间相当于scoped

  state: {  //可以定义很多类型 state: 1
    count: 2
  },
  //通俗理解就是监听函数,里面的函数可以随便定义名称然后依次执行
  subscriptions: {
    setup({ dispatch, history }) {  // eslint-disable-line
      window.onresize=() => {
        dispatch({
          type:'add'
        })
      }
    },
    onclick({ dispatch, history }) {  // eslint-disable-line
     document.addEventListener('click',()=>{
       dispatch({type: 'add'});
     })
    },
    setupHistory({ dispatch, history }) {  // eslint-disable-line
      history.listen((location)=>{
        // if(location.pathname === '/counter'){
        //   dispatch({type: 'add'});
        // }
        const match = pathToRegexp('/counter').exec(location.pathname)
        if(match){
          dispatch({type: 'add'});
        }
      })
     },
  },

  effects: {
    *asyncAdd({ payload }, { call, put, select }) {  // eslint-disable-line
      //const counter = yield select(state => state.counter)
      //const counter = yield select(_ => _.counter)
      //const {counter} = yield select(_ => _)
      const counter = yield select(({counter}) => counter) // eslint-disable-line
      yield call(delay,1000)
      yield put({ type: 'add' });
      console.log(querySting,12)
      yield put(routerRedux.push({
        pathname: '/',
        search: querySting.stringify({
          from: '深圳'
        })
      }));
    },
  },

  reducers: {
    add(state, action) {
      return {  
        count: state.count + 1
      }
    },
    // 'login/start'(state, action) {
    //   return {  
    //     count: state.count + 1
    //   }
    // }
  },

};
```
#### 实现一个简单的ajax
```
import axios from 'axios'
export default {

  namespace: 'user',

  state: {
    isFetching: false,
    error: null,
    user: null
  },

  subscriptions: {
    setup({ dispatch, history }) {  // eslint-disable-line
    },
  },

  effects: {
    *fetch(_, { call, put }) {  // eslint-disable-line
      yield put({ type: 'fetch/start' });
      try {
        const user = yield call(axios.get,'http://jsonplaceholder.typicode.com/users')
        yield put({type:'fetch/success',user:user})
      }catch(e){
        yield put({type: 'fetch/error',error:e.message})
      }
    },
  },

  reducers: {
    'fetch/start'(state, action) {
      return {
        isFetching: true,
        error: null,
        user: null
      }
    },
    'fetch/success'(state, action) {
      return {
        isFetching: false,
        error: null,
        user: action.user
      }
    },
    'fetch/error'(state, action) {
      return {
        isFetching: false,
        error: action.error,
        user: null
      }
    },
  },

};
```
```
import React from 'react'
import PropTypes from 'prop-types' // eslint-disable-line
import {connect} from 'dva'
const User = (props) => {
    const {isFetching, error, user} = props.user
    const{dispatch} = props
    let data
    if (error) {
        data  = error
    } else if(isFetching){
        data= 'loading...'

    }else{
        data = user&&user.data[0].name
    }
    return (
        <div>
            <h1>{data}</h1>
            <button onClick = {() => {dispatch({type:'user/fetch'})}}>get user</button>
        </div>
    )
}

User.propTypes = {}
const mapStateToProps = (state) => {
    return {user: state.user}
}
export default connect(mapStateToProps)(User)

```
