---
title: redux-saga
categories: "redux-saga"
tags:
  - react
  - es6
  - redux-saga
  - 递归
---
## 记录学习redux-saga(https://flaviocopes.com/redux-saga/)
> generator的使用
```
function* gen() {
var posts= yield $.getJSON("http://jsonplaceholder.typicode.com/posts")
console.log(posts[0]);
  var users= yield $.getJSON("http://jsonplaceholder.typicode.com/users")
console.log(users[0]);

}
run(gen)
function run(generator){
var mygen = generator();
function handle(yielded) {
  if(!yielded.done){
    yielded.value.then(function(data){
      return handle(mygen.next(data))
    })
  }
}
return handle(mygen.next())
}
```
![image](/img/1541078113.jpg)

> (https://flaviocopes.com/redux-saga/)Basic Helpers
takeEvery()
takeLatest()
take()
put()
call()
Running effects in parallel
all()
race()

```
// import {INCREMENT_ASYNC} from '../constants/counter'
import { takeEvery ,call,put,all} from 'redux-saga/effects'
import axios from 'axios'
import { delay} from 'redux-saga'
// import {increment} from '../actions/counter'
// const delay = (ms) => new Promise(resolve => setTimeout(resolve,ms))
function* incrementAsync() {
    // yield delay(2000);
    yield call(delay,2000);
    //yield put(increment())
    yield put({type:'INCREMENT'})
}
function* fetchUser() {
    const user = yield call(axios.get,'http://jsonplaceholder.typicode.com/users')
    // const [todos,user] = yield all([
    //     call(axios.get,'http://jsonplaceholder.typicode.com/todos'),
    //     call(axios.get,'http://jsonplaceholder.typicode.com/users'),
    // ])
    // console.log(todos)
    console.log(user)
}
function* watchIncrementAsync(){
    yield takeEvery("INCREMENT_ASYNC", incrementAsync);
}
function* watchFetchUser(){
    yield takeEvery("FETCH_USER_REQUEST", fetchUser);
}
export default function* rootSaga(){
    yield all([    //并发任务，同时执行不必等待
        watchIncrementAsync(),
        watchFetchUser()
    ])
}
```

> 组织代码不用一个个导入方式1
```
import { all, fork} from 'redux-saga/effects'
// import { watchIncrementAsync } from './counter'
// import { watchFetchUser,watchFetchTodos } from './user'
import * as counterSagas from './counter'
import * as userSagas from './user'

export default function* rootSaga(){
    // yield all([    //并发任务，同时执行不必等待
    //     watchIncrementAsync(),
    //     watchFetchUser(),
    //     watchFetchTodos()
    // ])
    yield all([    //并发任务，同时执行不必等待
        ...Object.values(counterSagas),
        ...Object.values(userSagas)
    ].map(fork)
)
}
```
> 组织代码不用一个个导入方式2
直接在各自文件最后
export const counterSaga = [
watchFetchUser(),
watchFetchTodos()
]
