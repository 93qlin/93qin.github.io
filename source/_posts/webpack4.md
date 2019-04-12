---
title: webpack4
categories: "webpack4"
tags:
  - javascript
  - webpack
---
> plugin: 扩张webpack功能的
  loader: 转换文件（js, css, 图片）
#### model: 模式配置
 > 可设置的值为mode：none，development或production（默认值）
 ** 用法 **
> 只需mode在配置中提供选项：
```
module.exports = {
  mode: 'production'
};
```
> 或者将其作为CLI参数传递：
```
webpack --mode=productio
```
#### 命令来指定打包文件到指定文件夹
```
webpack --mode=development ./src/hello.js --output ./build/main.js
```
> 命令很麻烦，所以需要配置文件package.json
```
npm init -y(-y的意思是不需要回车)
```

#### Getting Start
1.现在项目中装两个插件
```
npm install webpack webpack-cli --save-dev
```
#### 全局安装webapck的问题
```
The following NPM installation will make webpack available globally:
npm install --global webpack
Note that this is not a recommended practice. Installing globally locks you down to a specific version of webpack and could fail in projects that use a different version.
```
> 全局安装webpack会锁定webpack版本，当我们运行不同版本的webpack项目时会有问题
> 两种方法

** 1.自定义命令 devnow**
```
"scripts": {
    "devnow": "./node_modules/webpack/bin/webpack.js --mode=development",
  }
```
** 2.安装npx解决这个问题 **
> 只要是可执行文件前面都可以加npx,他首先会在当前项目里找不到就去全局找

```
npm install -g npx
```
```
"scripts": {
    "devnpx": "npx webpack --mode=development"
  },
```
#### webpack默认的配制文件（webpack-config.js）（全部自定义命令配置的话很长，不好看），
1.直接建个 webpack-config.js,因为webpack会默认去找这个文件。
```
moduls.exports = {
  mode: "development",
  entry: "./src/index.js",
  output: {
    path: path.resolve(__dirname, 'dist'), // __dirname: node.js里__dirname 总是指向被执行 js 文件的绝对路径所以当你在 /d1/d2/myscript.js 文件中写了 __dirname， 它的值就是 /d1/d2 。
    filename: "app.bundle.js"
  }
}
```
2. 然后自定义的命令就可以删了（删了devnpx命令里的--mode=development）
```
"scripts": {
    "devnpx": "npx webpack",
    "dev": "webpack --mode=development",
    "build": "webpack --mode=production"
  },
```
#### 配置多入口文件
编译多个js文件
```
const path = require('path')
module.exports = {
  mode: "development",
  entry:{
    app: "./src/index.js",
    hello: './src/hello.js'
  },
  output: {
    path: path.resolve(__dirname, 'dist'), // __dirname: node.js里__dirname 总是指向被执行 js 文件的绝对路径所以当你在 /d1/d2/myscript.js 文件中写了 __dirname， 它的值就是 /d1/d2 。
    filename: "[name].bundle.js"   // name是入口文件的名字作为key
  }
}
```
###### babel(js转换插件为了解决低版本的浏览器不支持新的js语法)
```
npm install --save-dev @babel/core // babel核心插件
npm install -D babel-loader // webpack和babel结合插件
```

#### babel插件（plugin）
```
arrow-functions
block-scoped-functions
block-scoping
classes
computed-properties
destructuring
duplicate-keys
for-of
function-name
instanceof
```
babel插件两种使用方法
```
.babelrc（项目根目录新建此文件，然后复制进去）

Without options:

{
  "plugins": ["@babel/plugin-transform-arrow-functions"]
}
```
```
With options:  （在webpack配置文件里加）

<!-- {
  "plugins": [
    ["@babel/plugin-transform-arrow-functions", { "spec": true }]
  ]
} -->

module: {
  rules: [
    {
      test: /\.m?js$/,
      exclude: /(node_modules|bower_components)/,
      use: {
        loader: 'babel-loader',
        options: {
          plugins: ['@babel/plugin-proposal-object-rest-spread']
        }
      }
    }
  ]
}
```

#### @babel/preset-env babel (preset)预设
> 作用： 解决每写一个js新语法都装一个插件比如箭头函数，class,
bable-env// 不包含装饰器插件，所以es6装饰器可能要加装
```
module: {
  rules: [
    {
      test: /\.m?js$/,
      exclude: /(node_modules|bower_components)/,
      use: {
        loader: 'babel-loader',
        options: {
          presets: ['@babel/preset-env',{debug: true}], //预设,包含某些es6常用插件,debug可以看到预设了那些插件
        }
      }
    }
  ]
}
```
#### @babel/polifill
bable能转换一些语法，但是一些新的api不能处理（Promise，，，）
原理： 往windows全局对象导入一些内容
// 安装略
{
  "presets": [["@babel/preset-env",{"debug": true, "useBuiltIns": "usage","corejs": "3.0.0"}]],
  "plugins": [
    ["@babel/plugin-proposal-decorators", { "legacy": true }],
  ]
}
**注意**
> 1."useBuiltIns": "usage" //按需编译
```
[/Users/qinlin/Desktop/webpack/hello-webpack-4/src/index.js] Added following core-js polyfills:
  es.array.from {}
  es.object.assign {}
  es.object.to-string {}
  es.promise {}
  es.string.iterator {}
```
> "corejs": "3.0.0"它依赖corejs需指定版本不然会报错

#### @babel/runtime
>  解决polifill全局污染的问题
```
polifill:
ar process = global.process;
var Promise = global.Promise;
```
所以不太好

> 用法：
npm install --save @babel/runtime
npm install --save-dev @babel/plugin-transform-runtime
npm install --save @babel/runtime-corejs2
@babel/plugin-transform-runtime的作用是避免重复的问题
比如：
```
function _classCallCheck(instance, Constructor) {
  //...
}

var Circle = function Circle() {
  _classCallCheck(this, Circle);
};
```
变为
```
var _classCallCheck = require("@babel/runtime/helpers/classCallCheck");

var Circle = function Circle() {
  _classCallCheck(this, Circle);
};
```
配置一下
```
{
  "presets": [["@babel/preset-env",{"debug": true}]],
  "plugins": [
    [
      "@babel/plugin-proposal-decorators", { "legacy": true }
    ],
    [
      "@babel/plugin-transform-runtime",
      {
        "absoluteRuntime": false,
        "corejs": 2,
        "helpers": true,
        "regenerator": true,
        "useESModules": false
      }
    ]
  ]
}
```
#### 搭建react环境
1.npm install react react-dom
2. 预设npm install --save-dev @babel/preset-react

#### plugin(插件作用扩展webpack功能)
**html-webpack-plugin**（可以自动生成html,可以解决浏览器缓存问题，因为每次文件名都会更新）
npm i --save-dev html-webpack-plugin

```
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
  ....
  plugins: [
    new HtmlWebpackPlugin()
  ]
}

```
**可以加参数**
```
new HtmlWebpackPlugin({
  title: 'admin',
  filename: 'admin/index.html',  // 放在dist/admin目录下
  template: 'public/index.html',  // 模板文件
  chunks: ["admin"]              // 指定chunk
})
```
**配资多个html**
```
plugins: [
  new HtmlWebpackPlugin({
    title: 'admin',
    filename: 'admin/index.html',
    template: 'public/index.html',
    chunks: ["admin"]
  }),
  new HtmlWebpackPlugin({
    title: 'index',
    filename: 'app/index.html',
    template: 'public/index.html',
    chunks: ["app"]
  })
]
```
#### css-loader
```
npm install sass-loader node-sass  --save-dev
```
less等略
最终
> webapck.config.js
```
const path = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')
module.exports = {
  // mode: "development",
  entry:{
    // app: ["@babel/polyfill","./src/index.js"],
    app: "./src/index.js",
    admin: './src/hello.js'
  },
  devtool: false,
  output: {
    path: path.resolve(__dirname, 'dist'), // __dirname: node.js里__dirname 总是指向被执行 js 文件的绝对路径所以当你在 /d1/d2/myscript.js 文件中写了 __dirname， 它的值就是 /d1/d2 。
    filename: "[name]/[hash].bundle.js",   // name是入口文件的名字作为key
    // chunkFilename: "[name]-[hash].[id].bundle.js",   // name是入口文件的名字作为key
  },
  module: {
    rules: [
      {
        test: /\.m?js$/,
        exclude: /(node_modules|bower_components)/,
        use: {
          loader: 'babel-loader',
          // options: {
          //   presets: [['@babel/preset-env',{debug: true}]], //预设,包含某些es6常用插件,debug可以看到预设了那些插件
          // }
        }
      },
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader'],  // 右边到左边执行css => style
      },
      {
        test: /\.(scss|sass)$/,
        use: [
          "style-loader", // creates style nodes from JS strings
          "css-loader", // translates CSS into CommonJS
          "sass-loader" // compiles Sass to CSS, using Node Sass by default
        ]
      }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({
      title: 'admin',
      filename: 'admin/index.html',
      template: 'public/index.html',
      chunks: ["admin"]
    }),
    new HtmlWebpackPlugin({
      title: 'index',
      filename: 'app/index.html',
      template: 'public/index.html',
      chunks: ["app"]
    })
  ]
}
```
#### mini-css-extract-plugin把js释放css文件
```
 npm install --save-dev mini-css-extract-plugin
```
```
webpack.config.js

const MiniCssExtractPlugin = require("mini-css-extract-plugin");
const devMode = process.env.NODE_ENV !== 'production'

module.exports = {
  plugins: [
    new MiniCssExtractPlugin({
      // Options similar to the same options in webpackOptions.output
      // both options are optional
      filename: devMode ? '[name].css' : '[name].[hash].css',
      chunkFilename: devMode ? '[id].css' : '[id].[hash].css',
    })
  ],
  module: {
    rules: [
      {
        test: /\.(sa|sc|c)ss$/,
        use: [
          devMode ? 'style-loader' : MiniCssExtractPlugin.loader,
          'css-loader',
          'postcss-loader',
          'sass-loader',
        ],
      }
    ]
  }
}
```
#### webpack-dev-server(开启一个本地服务器，热加载，自动打开浏览器)
```
npm install webpack-dev-server --save-dev
```
```
"devnpx": "npx webpack-dev-server --open",
```

#### clean-webpack-plugin(清空之前生成的hash文件)
```
npm install --save-dev clean-webpack-plugin
```
```
npm install -g serve
```
```
let pathToClean = ['dist']
....
new CleanWebpackPlugin({pathToClean:pathToClean})

```
执行命令
```
serve -s dist
```
#### 处理图片
```
npm install file-loader --save-dev
```
#### 压缩图片
```
npm install image-webpack-loader --save-dev
```
查看文件体积命令
ls -lh src

最终webpack.config.js
```
const path = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
const devMode = process.env.NODE_ENV !== 'production'
const CleanWebpackPlugin = require('clean-webpack-plugin');
let pathToClean = ['dist']
module.exports = {
  // mode: "development",
  entry:{
    // app: ["@babel/polyfill","./src/index.js"],
    app: "./src/index.js",
    admin: './src/hello.js'
  },
  devtool: false,
  output: {
    path: path.resolve(__dirname, 'dist'), // __dirname: node.js里__dirname 总是指向被执行 js 文件的绝对路径所以当你在 /d1/d2/myscript.js 文件中写了 __dirname， 它的值就是 /d1/d2 。
    // filename: "[name]/[hash].bundle.js",   // name是入口文件的名字作为key
    filename: "js/[name]-[hash].bundle.js",   // name是入口文件的名字作为key
  },
  module: {
    rules: [
      {
        test: /\.m?js$/,
        exclude: /(node_modules|bower_components)/,
        use: {
          loader: 'babel-loader',
          // options: {
          //   presets: [['@babel/preset-env',{debug: true}]], //预设,包含某些es6常用插件,debug可以看到预设了那些插件
          // }
        }
      },
      {
        test: /\.css$/,
        use: [
          devMode ? 'style-loader' : MiniCssExtractPlugin.loader,
          'css-loader'
        ],  // 右边到左边执行css => style
      },
      {
        test: /\.(scss|sass)$/,
        use: [
          devMode ? 'style-loader' : MiniCssExtractPlugin.loader, // creates style nodes from JS strings
          "css-loader", // translates CSS into CommonJS
          "sass-loader" // compiles Sass to CSS, using Node Sass by default
        ]
      },
      {
        test: /\.(png|jpe?g|gif)$/,
        use: [
          {
            loader: 'file-loader',
            options: {
              name: '[name].[ext]',
              outputPath: 'images/',
            }
          },
          {
            loader: 'image-webpack-loader',
          }
        ]
      }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({
      title: 'admin',
      filename: 'admin.html',
      template: 'public/index.html',
      chunks: ["admin"]
    }),
    new HtmlWebpackPlugin({
      title: 'index',
      filename: 'index.html',
      template: 'public/index.html',
      chunks: ["app"]
    }),
    new HtmlWebpackPlugin({
      title: 'index',
      filename: 'index.html',
      template: 'public/index.html',
      chunks: ["app"]
    }),
    new MiniCssExtractPlugin({
      // Options similar to the same options in webpackOptions.output
      // both options are optional
      filename: devMode ? 'css/[name].css' : 'css/[name].[hash].css',
      chunkFilename: devMode ? 'css/[id].css' : 'css/[id].[hash].css',
    }),
    new CleanWebpackPlugin({pathToClean:pathToClean})
  ]
}
```
.babelrc
```
{
  "presets": [
    [
      "@babel/preset-env",{"debug": true}
    ],
    [
      "@babel/preset-react"
    ]
  ],
  "plugins": [
    [
      "@babel/plugin-proposal-decorators", { "legacy": true }
    ],
    [
      "@babel/plugin-transform-runtime",
      {
        "absoluteRuntime": false,
        "corejs": 2,
        "helpers": true,
        "regenerator": true,
        "useESModules": false
      }
    ]
  ]
}
```
