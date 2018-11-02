
---
title: vue问题，技巧提升
categories: "vue问题，技巧提升"
tags:
  - javascript
  - vue
---

> 用了vue已有将近两年了，一直没做过总结只知道做项目，遇到类似的问题不记得有时还得去翻之前的项目，偶然看见别人在做了一个项目就做了总结很是汗颜，所以想着写一篇博客来总结（当然也有许多地方是复制粘贴别人的）

### timeago.js将时间戳转换成“几天前”“几分钟前”等格式

第一步：安装timeago.js
```
npm install timeago.js
```
或
cnpm install timeago.js(推荐)
第二步：引入timeago.js
```
import timeago from 'timeago.js';
```
第三步：使用
本例中讲述如何在vue中使用
//1.在vue分过滤器中定义changeTime方法
```
filters: {
    //timeago.js插件
    //计算时间，类似于几分钟前，几小时前，几天前等
    changeTime(val){
        let time = new Date(val); //先将接收到的json格式的日期数据转换成可用的js对象日期
        return new timeago().format(time, 'zh_CN'); //转换成类似于几天前的格式
    }
}
```
//2.在template中使用，例如：
```
//time是需要转换的时间戳，changeTime是过滤器中定义的方法
<span>{{time | changeTime}}</span>
```

### 路由传参

vue传参方式有：query、params+动态路由传参。说下两者的区别：    1.query通过path切换路由，params通过name切换路由// query通过path切换路由
```
<router-link :to="{path: 'Detail', query: { id: 1 }}">前往Detail页面</router-link>
// params通过name切换路由
<router-link :to="{name: 'Detail', params: { id: 1 }}">前往Detail页面</router-link>
```
