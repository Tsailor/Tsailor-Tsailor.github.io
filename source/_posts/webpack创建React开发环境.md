## 总结 ##  
各功能具体实现过程见readMe.md  

### 1 webpack搭建react项目 ###  
以前总是使用脚手架工具直接创建，今天试试webpack手动创建react 项目 

1、 新建src 文件夹存放react代码  ```mkdir src```
2、 初始化项目   ```npm init -y ```   
3、 安装webpack ```npm i webpack webpack-cli -dev ```    
webpack和babel在打包的时候不会包括到源码里，所以是 - div  
系统提示说："-dev"已经不推荐使用了，使用 "--only=dev"代替  
webpack-cli 包含了许多webpack的指令。  
4、 安装babel     
```npm install @babel/core babel-loader @babel/preset-env @babel/preset-react --only=dev```  
@babel/core 这是babel的核心库   
@babel/preset-env  将es6编译成es5  
@babel/preset-react  识别JSX语法
babel-loader     将经过babel处理后的文件输出成浏览器可以识别的格式  
5、 配置babel，根目录下新建.babelrc文件  
写入
```
{
  "presets": ["@babel/preset-env", "@babel/preset-react"]
}
```  
6、 配置webpack, 新建 ```webpack.config.js ``` 
```javascript
const path = require('path');  //引入路径

module.exports = {      //  导出
    entry: './src/main.js',    //    项目入口
    output: {                  //    输出 
      filename: 'bundle.js',    //    打包后的名字
      path: path.resolve(__dirname, 'dist')   //  当前路径下
    },
    module: {
      rules: [
        {
          test: /\.js$/,         //  打包 .js文件
          exclude: /node_modules/,
          use: {
            loader: "babel-loader"    //  使用 babel-loader
          }
        }
      ]
    }
  };  
```  
7、解析HTML文件，webpack的默认配置只能解析.js文件。安装``` html-webpack-plugin```  
```javascript
npm i html-webpack-plugin --only=dev
```
再配置插件 
```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin')  // 引入
plugins: [
    new HtmlWebpackPlugin({
        title: 'load-files',
        template: 'index.html'   //  模板
    })
]    
```
8、安装react、react-dom  
```npm install react react-dom --save```  
9、 安装，启动webpack-dev-server  
```javascript 
npm install webpack-dev-server --save-dev
```    
package.json中，scripts标签里加入  
```javascript 
 "start:dev": "webpack-dev-server"
``` 
10、执行``` npm run start:dev```  
11、打包命令，npx webpack   
12、 配置解析css的loader  ,```npm install --save-dev css-loader style-loader```顺序先下后上
```javascript
 [
            { loader: 'style-loader' },   //  将style插入到模板里
            {
              loader: 'css-loader',    //   解析css
              options: {
                modules: true
              }
            }
          ]
```  
13、抽离css样式到独立的文件```javascript npm install --save-dev mini-css-extract-plugin```    
  配置参照npm文档  




