---
title: webpack
categories: "webpack"
tags:
  - webpack
---
# webpack
# 基础
1 .初始化： npm init
2 .安装： npm install webpack --save-dev
## webpack处理js文件
命令： webpack ./hello.js hello.bunder.js（生成的文件名）
## webpack处理css文件
需要安装loader: npm install  css-loader style-loader -s
注意在其他文件引入时： require('style-loader!css-loader!./style.css')
css-loader: 处理css文件
style-loader： 把处理的css文件新建style标签插入html

--module-bind：如果每引入一个css文件都加前面几句麻烦，也可以在命令行直接里利用参数：
--module-bind使用（注意双引号）： webpack ./hello.js hello.bunder.js --module-bind "css=style-loader!css-loader"

--watch解决每一次改变文件都要运行命令麻烦
 webpack ./hello.js hello.bunder.js --module-bind "css=style-loader!css-loader" --watch
 --progress(打包过程)
 --display-modules(打包模块)
 --display-reasons(打包理由)
 --colors(彩色字体)

# 构建项目
    npm init
    npm install webpack --save-dev
    mkdir src(建文件夹)
    。。。
    建立webpack.config.js文件配置（也可以别的名字但是要使用 webpack --config 文件名）
    运行 webpack
    webpack时可以加参数（在packagejson/scripts定义："webpack": "webpack --config webpack.config.js --progress --display-modules --color --display-reasons"）

# entry改为数组或对象
entry: ['./src/script/main.js','./src/script/a.js'],
entry: {main: './src/script/main.js',a: './src/script/a.js'},
对象的话filename会覆盖解决方式 filename: '[name].js',
[name] is replaced by the name of the chunk.
[hash] is replaced by the hash of the compilation.
[chunkhash] is replaced by the hash of the chunk.（每个chunk不一样，当chunk改变其跟着也会改变）

# html-webpack-plugin(生成html可以避免每次都要改路径比如用的chunkhash)
npm install html-webpack-plugin --save-dev
var htmlWebpackPlugin = require('html-webpack-plugin')
。。。。
plugins: [
    new htmlWebpackPlugin()
  ]
## 此时生成了html而且关联了相关js文件，但还是无法和根目录的index.html相关联
所以加个参数相关联index.html改变生成的html也会改变
```
plugins: [
    new htmlWebpackPlugin({
      template: 'index.html'
    })
  ]
```
## 改变路径可以做到html和js文件分离
```
output: {
    path: __dirname + '/dist',
    filename: 'js/[name]-[chunkhash].js'
  },
```
## 可以指定生成文件名和脚本放置位置
```
plugins: [
    new htmlWebpackPlugin({
      filename: 'index-[hash].html',
      template: 'index.html',
      inject: 'head' //把脚本放在heed标签里，默认放在body里
    })
  ]
```
## 配置模板
```
    plugins: [
    new htmlWebpackPlugin({
      filename: 'index.html',
      template: 'index.html',
      inject: 'head', //把脚本放在heed标签里，默认放在body里
      title: '标题1',
      date: new Date()
    })
  ]
```
### 模板写法以及遍历
```
    <%= %>取值
    <% %>执行js代码
    在index.html<title><%= htmlWebpackPlugin.options.title %></title>
    <body>
  <%= htmlWebpackPlugin.options.date %>
  <% for (var key in htmlWebpackPlugin.files) { %>
    <%= key %> : <%= JSON.stringify(htmlWebpackPlugin.files[key]) %>
  <% } %>
  <% for (var key in htmlWebpackPlugin.options) { %>
    <%= key %> : <%= JSON.stringify(htmlWebpackPlugin.options[key]) %>
  <% } %>
</body>
```
在webpack./config.js
```
plugins: [
    new htmlWebpackPlugin({
      filename: 'index.html',
      template: 'index.html',
      inject: 'head', //把脚本放在heed标签里，默认放在body里
      title: '标题1',
      date: new Date()
    })
  ]
```
### 设置引入js文件的位置或body或head
#### 在模板index.html里设置script的属性（注意把inject设为false）
```
 <script src="<%= htmlWebpackPlugin.files.chunks.a.entry %>"></script>
 plugins: [
    new htmlWebpackPlugin({
      filename: 'index.html',
      template: 'index.html',
      inject: false, //'head'把脚本放在heed标签里，默认放在body里
      title: '标题1',
      date: new Date()
    })
  ]
```
### 上线路径（publicPath）
    output: {
    path: __dirname + '/dist',
    filename: 'js/[name]-[chunkhash].js',
    publicPath: 'http://xxx.com/'
  }
### 压缩html（minify）
    plugins: [
    new htmlWebpackPlugin({
      filename: 'index.html',
      template: 'index.html',
      inject: false,//'head'把脚本放在heed标签里，默认放在body里
      title: '标题1',
      date: new Date(),
      minify: {
        removeComments: true, //删除注释
        collapseWhitespace: true //删除空格
      }
    })
  ]
# 多页面配置（chunks,excludeChunks）
#### chunks(包含XXX)
```
new htmlWebpackPlugin({
      filename: 'b.html',
      template: 'index.html',
      inject: 'body',//'head'把脚本放在heed标签里，默认放在body里
      title: '标题b',
      date: new Date(),
      chunks: ['b'],
      minify: {
        removeComments: true, //删除注释
        collapseWhitespace: true //删除空格
      }
    })
```
#### excludeChunks(除了XXX都包含)
```
new htmlWebpackPlugin({
      filename: 'b.html',
      template: 'index.html',
      inject: 'body', //'head'把脚本放在heed标签里，默认放在body里
      title: '标题b',
      date: new Date(),
      excludeChunks: ['a', 'c'],
      minify: {
        removeComments: true, //删除注释
        collapseWhitespace: true //删除空格
      }
    })
```
# 设置了publicPath: http://.....则生成的文件就会加巨http请求。
#### inline引入(模板html)
```
    <script>
    <%= compilation.assets[htmlWebpackPlugin.files.chunks.main.entry.substr(htmlWebpackPlugin.files.publicPath.length)].source()%>
  </script>
```
### webpack配置
```
 new htmlWebpackPlugin({
      filename: 'a.html',
      template: 'index.html',
      inject: false, //'head'把脚本放在heed标签里，默认放在body里,false关闭
      title: '标题a',
      date: new Date(),
      /*chunks: ['main','a'], //指定生成的js*/
      excludeChunks: ['b', 'c']
    }),
```
### index.html最终版
```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title><%= htmlWebpackPlugin.options.title %></title>
  <script>
    <%= compilation.assets[htmlWebpackPlugin.files.chunks.main.entry.substr(htmlWebpackPlugin.files.publicPath.length)].source()%>
  </script>
</head>
<body>
  <% for (var key in htmlWebpackPlugin.files.chunks) { %>
    <% if (key != 'main') { %>
      <script src="<%= htmlWebpackPlugin.files.chunks[key].entry %>"></script>
    <% } %>
  <% } %>
</body>
</html>
```
# loader(处理资源文件，es6,7 less,图片，，，，)
## babel-loader:
```
npm install --save-dev babel-loader babel-core
npm install --save-dev babel-preset-latest(es5,6,7,,,)

module: {
    rules: [
      {
        test: /\.js$/,
        loader: 'babel-loader',
        exclude: '/node_modules/', //排除打包文件优化打包时间
        include: '/src/', //定义打包文件
        query: {
          'presets': ['latest']
        }
      }
    ]
  }
```
## postcss-loader;（处理css）
```
 npm install postcss-loader --save-dev
 npm install autoprefixer --save-dev(加前缀)
 npm install postcss-import --save-dev(解决import css文件时生成多个style)
```
## less-loader
```
npm i less --save-dev
npm i less-loader --save-dev
```

## 处理模板文件(html,ejs,,,,,,)
` npm install html-loader --save-dev `
