---
title: WebPack系列1：WebPack5搭建React+TS+antd+less+mobx开发环境
tags: 
- tools
- webpack
date: 2021-11-17
---



一直以来使用 React 开发项目都是使用脚手架 umi,这次试试使用 WebPack5 搭建 React+TS+antd+less 开发环境。

**项目初始化**
新建文件夹并进入此文件夹，打开命令工具(cmd / git bash)
`yarn init -y`

**Webpack**
`yarn add webpack webpack-cli -D`

[webpack-cli](https://webpack.docschina.org/api/cli/)提供了许多命令来使 webpack 的工作变得更简单

本文安装的版本是

```json
"webpack": "^5.64.1",
"webpack-cli": "^4.9.1"
```

创建 Webpack 配置文件
`webpack.config.js`
配置基本内容

<!--more-->

```javascript
const path = require("path");

module.exports = {
  mode: "development", // 指定 development(开发环境) 和 production(生产环境)
  entry: {
    index: "./src/index.js", // 项目入口
  },
  output: {
    filename: "[name].bundle.js", // 输出文件名
    path: path.resolve(__dirname, "dist"), // 路径
    clean: true, // 打包清除缓存
  },
};
```

**React**
安装 React、ReactDom
`yarn add react react-dom -S`  
安装解析 ES6 和 JSX 语法的 Babel  
babel-loader@7+ 对应的版本都更名（以前是 xx-yy-zz的格式） @babel/core  @babel/preset-env @babel/preset-react
`yarn add @babel/core babel-loader @babel/preset-env @babel/preset-react -D`  
根目录下创建.babelrc 文件，并写入

```javascript
{
    "presets": [
        "@babel/preset-env",
        "@babel/preset-react"
    ]
}
```

**配置 Webpack**
安装开发服务器
`yarn add webpack-dev-server -D`
配置 devtool 和 devServer

```javascript
//  ...  省略其他

module.exports = {
  //  ...  省略其他
  devtool: "inline-source-map",
  devServer: {
    static: "./dist",
    port: "3001",
  },
};
```

安装插件 HtmlWebpackPlugin, 并且调整 webpack.config.js 文件：
`yarn add html-webpack-plugin -D`

并配置 babel-loader

```javascript
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");

module.exports = {
{
    //  ...  省略其他
   plugins: [
     new HtmlWebpackPlugin({
       title: 'React——New Test',
     }),
   ],
    module: {
    rules: [
      {
        test: /\.(js|jsx)?$/, // 处理以.js|.jsx结尾的文件
        exclude: /node_modules/, // 处理除了nodde_modules里的js文件
        loader: "babel-loader", // 用babel-loader处理
      }
    ],
}

```

**配置 Npm Script**
在 package.json 中

```json
  "scripts": {
    "build": "webpack",
    "start": "webpack serve --open"
  },
```

**配置 HTML 模板**
新建 public 文件夹
加入 Html 文件 、favicon 图标
index.html

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title><%= htmlWebpackPlugin.options.title %></title>
  </head>
  <body>
    <div id="root"></div>
  </body>
</html>
```

更新 HtmlWebpackPlugin 的配置

```javascript
module.export = {
  // ... 省略其他
  plugins: [
    new HtmlWebpackPlugin({
      title: "React——NEW Test",
      template: "public/index.html",
      favicon: "public/favicon.png",
    }),
  ],
};
```

**写入 React 项目代码**
新建 src 文件夹，新建 index.js 文件作为项目入口
写入

```javascript
import React from "react";
import ReactDOM from "react-dom";

const App = () => {
  return <div>hello,world</div>;
};
ReactDOM.render(<App />, document.querySelector("#root"));
```

**运行**
终端输入命令 ` yarn start`,如果项目跑起来了则一切正常

**使用 less 文件**
安装插件 mini-css-extract-plugin， 将 CSS 提取到单独的文件中，为每个包含 CSS 的 JS 文件创建一个 CSS 文件
安装 css-loader、less-loader 并开启模块化

`yarn add mini-css-extract-plugin css-loader less less-loader -D`

```javascript
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
  // ...省略
  plugins: [
    new HtmlWebpackPlugin({
      title: "React——NEW Test",
      template: "public/index.html",
      favicon: "public/favicon.png",
    }),
    new MiniCssExtractPlugin(),
  ],
  module: {
    rules: [
      {
        test: /\.(js|jsx)$/, // 处理以.js|.jsx结尾的文件
        exclude: /node_modules/, // 处理除了nodde_modules里的js文件
        loader: "babel-loader", // 用babel-loader处理
      },
      {
        test: /\.css$/i,
        use: [
          MiniCssExtractPlugin.loader,
          {
            loader: "css-loader",
            options: {
              modules: true,
            },
          },
        ],
      },
      {
        test: /\.less$/i,
        use: [
          MiniCssExtractPlugin.loader,
          {
            loader: "css-loader",
            options: {
              modules: true,
            },
          },
          {
            loader: "less-loader",
          },
        ],
      },
    ],
  },
};
```

**安装 TypeScript**

添加 React 和 React-DOM 的声明文件
`yarn add @types/react @types/react-dom -D`

添加 TypeScript 的依赖 ,*特别注意不要使用 awesome-typescript-loader* , 这个已经淘汰了和新的很多 库不兼容，例如 antd.
使用这个babel-loader `@babel/preset-typescript`

`yarn add typescript @babel/preset-typescript source-map-loader -D`

添加 TypeScript 配置文件
新建 tsconfig.json 文件 写入

```json
{
  "compilerOptions": {
    "outDir": "./dist/",
    "sourceMap": true,
    "noImplicitAny": true,
    "module": "commonjs",
    "target": "es5",
    "jsx": "react",
    "allowJs": false,
    "allowSyntheticDefaultImports": true,
    "esModuleInterop": true
  },
  "include": ["./src/**/*"],
  "exclude": ["node_modules"]
}
```
关于allowSyntheticDefaultImports见这里 [由 allowSyntheticDefaultImports 引起的思考](https://blog.leodots.me/post/40-think-about-allowSyntheticDefaultImports.html)

再配置 .babelrc文件。
```json
{
  "presets": [
    "@babel/preset-env",
    "@babel/preset-react",
    "@babel/preset-typescript"   // 新增一行
  ]
}
```

再修改 配置 webpack.config.js文件。
```javascript
   // ... 省略
    {
        test: /\.(ts|tsx|js|jsx)$/,
        exclude: /node_modules/, // 处理除了nodde_modules里的js文件
        loader: "babel-loader", // 用babel-loader处理
    }, 
  // 省略
```
将 index.js 以及 webpack 的入口都修改为 index.tsx。
如果有引入 css/less 图片等内容的话
然后发现 index.tsx 里 import 语句有报错。
`Cannot find module './styles.less' or its corresponding type declarations.ts(2307)`

_在 typescript 中是无法识别非代码资源的，所以会报错 TS2307: cannot find module '.png'。因此，我们需要主动的去声明这个 module。新建一个 ts 声明文件_

src 目录下，新建 type.d.ts,并写入

```typescript
declare module "*.svg";
declare module "*.png";
declare module "*.jpg";
declare module "*.jpeg";
declare module "*.gif";
declare module "*.bmp";
declare module "*.tiff";

declare module "*.less";
declare module "*.css";
```
运行一下，看看效果

**使用Antd**
安装antd组件库，并开启按需加载
```
yarn add antd -S
yarn add babel-plugin-import -D
```
配置.babelrc文件
```json
{
    // ...省略
  // 新增以下内容  
 "plugins": [
    [
      "import",
      {
        "libraryName": "antd",
        "libraryDirectory": "lib",
        "style": true
      }
    ]
  ]
}
```
关于配置、总共有三种配置代码
1. `["import", { "libraryName": "antd" }]`：引入的是 js 模块

2. `["import", { "libraryName": "antd", "style": true }]`：引入的是 less 文件，需要在 webpack.config.js 额外配置 less-loader 等，且不能在 module.rules 中把 node_modules/antd 给 exclude 了，如下操作

3. `["import", { "libraryName": "antd", "style": css }]`：引入的是 css 文件，需要在 webpack.config.js 额外配置 css-loader 等，且不能在 module.rules 中把 node_modules/antd 给 exclude 

**开启了CSS 模块化后，和antd的按需加载 发生冲突，导致样式无效**

利用 `exclude: /node_modules/`, 分开配置，在webpack.config.js中修改配置
```javascript
      {
        test: /\.css$/i,
        exclude: /node_modules/,
        use: [
          MiniCssExtractPlugin.loader,
          {
            loader: "css-loader",
            options: {
              modules: true,
              importLoaders: 1,
            },
          },
        ],
      },
      {
        test: /\.less$/,    // node_modules中的内容关闭模块化
        exclude: [/src/],
        use: [
          MiniCssExtractPlugin.loader,
          {
            loader: "css-loader",
            options: {
              importLoaders: 1,
            },
          },
          {
            loader: "less-loader",
            options: {
              lessOptions: {
                javascriptEnabled: true,
              },
            },
          },
        ],
      },
      {
        test: /\.less$/i,
        exclude: /node_modules/,    // src中的内容关闭模块化
        use: [
          MiniCssExtractPlugin.loader,
          {
            loader: "css-loader",
            options: {
              modules: true,
              importLoaders: 1,
            },
          },
          {
            loader: "less-loader",
          },
        ],
      },
```

然后运行 npm start, 可能会遇到这个错误, “内联JavaScript没有启用。”这是less的问题，在less低版本里没有这个问题
```
ERROR in ./node_modules/antd/es/button/style/index.less (./node_modules/antd/es/button/style/index.less.webpack[javascript/auto]!=!./node_modules/css-loader/dist/cjs.js??ruleSet[1].rules[2].use[1]!./node_modules/less-loader/dist/cjs.js!./node_modules/antd/es/button/style/index.less)

Module build failed (from ./node_modules/less-loader/dist/cjs.js):

// https://github.com/ant-design/ant-motion/issues/44
.bezierEasingMixin();
^
Inline JavaScript is not enabled. Is it set in your options?
```
解决方式, 在less-loader中加入配置，允许javascript
```
 {
    loader: "less-loader",
    options:{
        lessOptions:{
            javascriptEnabled:true
    }
},
```
如果你遇到了这个问题，那也是因为你webpack.config.js 的antd 没有分开配置
```
ERROR in ./node_modules/antd/lib/style/index.less 1:0
Module parse failed: Unexpected character '@' (1:0)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
> @import './themes/index';

```

至此，WebPack5 搭建 React+TS+antd+less 开发环境 暂且完成。

接下来项目中引入mobx状态管理库
## mobx##

安装 `yarn add mobx mobx-react -S` 
安装 `yarn add @babel/plugin-proposal-decorators -D` 解析装饰器语法的loader

配置 .babelrc文件
```
{

  "plugins": [
    // ...省略其他
    
    // 新增以下内容
    [
      "@babel/plugin-proposal-decorators",
      {
        "legacy": true
      }
    ]
  ]
}
```

配置tsconfig.json 中,开启实验阶段的装饰器语法
"compilerOptions"中加入 `"experimentalDecorators":true,`
愿天堂没有配置
⛺⛺⛺⛺⛺⛺⛺⛺⛺⛺⛺⛺⛺⛺

<details>
<summary>附录：webpack.config.js</summary>
<br>
```
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
  mode: "development",
  entry: {
    index: "./src/index.tsx",
  },
  output: {
    filename: "[name].bundle.js",
    path: path.resolve(__dirname, "dist"),
    clean: true, //清理 /dist 文件夹
  },
  devtool: "inline-source-map",
  resolve: {
    // Add '.ts' and '.tsx' as resolvable extensions.
    extensions: [".ts", ".tsx", ".js", ".json"],
  },
  devServer: {
    static: "./dist",
    port: "3001",
    open: false,
  },

  plugins: [
    new HtmlWebpackPlugin({
      title: "React——NEW Test",
      template: "public/index.html",
      favicon: "public/favicon.png",
    }),
    new MiniCssExtractPlugin(),
  ],

  module: {
    rules: [
      {
        test: /\.(ts|tsx|js|jsx)$/,
        exclude: /node_modules/, // 处理除了nodde_modules里的js文件
        loader: "babel-loader", // 用babel-loader处理
      },
      {
        test: /\.css$/i,
        exclude: /node_modules/,
        use: [
          MiniCssExtractPlugin.loader,
          {
            loader: "css-loader",
            options: {
              modules: true,
              importLoaders: 1,
            },
          },
        ],
      },
      {
        test: /\.less$/,    // node_modules中的内容关闭模块化
        exclude: [/src/],
        use: [
          MiniCssExtractPlugin.loader,
          {
            loader: "css-loader",
            options: {
              importLoaders: 1,
            },
          },
          {
            loader: "less-loader",
            options: {
              lessOptions: {
                javascriptEnabled: true,
              },
            },
          },
        ],
      },
      {
        test: /\.less$/i,
        exclude: /node_modules/,
        use: [
          MiniCssExtractPlugin.loader,
          {
            loader: "css-loader",
            options: {
              modules: true,
              importLoaders: 1,
            },
          },
          {
            loader: "less-loader",
            options: {
              lessOptions: {
                javascriptEnabled: true,
              },
            },
          },
        ],
      },
      {
        test: /\.(png|svg|jpg|jpeg|gif)$/i,
        type: "asset/resource",
      },
    ],
  },
};
```
</details>