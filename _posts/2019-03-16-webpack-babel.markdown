---
layout:     post
title:      "webpack、babel的基本使用"
subtitle:   "babel webpack javascript"
date:       2019-03-16
author:     "xiefeisd"
header-img: "img/post-bg.jpg"
tags:
    - 工具使用
    - 踩坑
---

# 1 安装node.js和npm

下载node-v10.15.3-linux-x64.tar.xz，上传到~

$ cd ~ /* 进入home */

$ tar xf node-v10.15.3-linux-x64.tar.xz /* 解压缩 */

$ cd node-v10.15.3-linux-x64 /* 进入目录 */

$ node -v /* 查看版本 */

$ sudo mv node-v10.15.3-linux-x64 /usr/local /* 移动文件夹 */

$ sudo ln -s /usr/local/node-v10.15.3-linux-x64/bin/node /usr/bin/node /* 建立软连接 */

$ sudo ln -s /usr/local/node-v10.15.3-linux-x64/bin/npm /usr/bin/npm /* 建立软连接 */

$ sudo ln -s /usr/local/node-v10.15.3-linux-x64/bin/npx /usr/bin/npx /* 建立软连接 */

# 2 建立项目

$ mkdir webpack-demo /* 建立根目录 */

$ cd webpack-demo /* 进入根目录 */

$ mkdir src /* 建立源代码目录 */

$ mkdir dist /* 建立发布目录 */

$ npm init /* 初始化项目 */

# 3 安装webpack

$ npm install --save-dev webpack /* 安装webpack */

$ npm install --save-dev webpack-cli /* 安装webpack-cli */

# 4 使用webpack

## 4.1 安装lodash（演示所需）

npm install --save lodash

## 4.2 准备文件

文件结构
```
webpack-demo
  |- package.json
  |- /dist
    |- index.html
  |- /src
    |- index.js
```
src/index.js：
```
import _ from 'lodash';
function component() {
  var element = document.createElement('div');
  element.innerHTML = _.join(['Hello', 'webpack'], ' ');
  return element;
}
document.body.appendChild(component());
```
dist/index.html：
```
<!doctype html>
<html>
  <head>
    <meta charset=utf-8>
    <title>test</title>
  </head>
  <body>
    <script src="main.js"></script>
  </body>
</html>
```
package.json：
```
{
  "name": "webpack-demo",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "webpack": "^4.29.6",
    "webpack-cli": "^3.3.0"
  }
}
```
$ npx webpack /* 运行打包 */

## 4.3 访问测试

用浏览器访问dist/index.html，显示hello webpack。

# 5 在webpack中使用babel

## 5.1 安装babel

$ npm install --save-dev babel-core babel-loader@7 /* 安装babel */

$ npm install --save-dev babel-preset-env /* 安装babel-preset-env */

## 5.2 配置babel

$ vim .babelrc /* 建立.babelrc文件，内容如下 */

{
  "presets": ["env"]
}

## 5.3 配置webpack

$ vim webpack.config.js /* 创建配置文件 */
```
module.exports = {
    entry: './src/index.js',
    output:
    {
        path: __dirname + "/dist",
        filename: 'bundle.js'
    },
    module:
    {
        rules: [
        {
            test: /\.js$/,
            exclude: /node_modules/,
            loader: "babel-loader"
        }]
    }
};
```

## 5.4 准备ES6文件

$ vim src/goudan.js /* 建立源代码文件，内容如下 */
```
export class Gou{
    constructor(){
        this.name = "goudan";
    };    
    sayHello(){
        return "Hello, I am goudan.";
    };
}
```

## 5.5 修改入口文件

src/index.js:
```
import _ from 'lodash';
import {Gou} from "./goudan.js";
function component() {
  var element = document.createElement('div');
  element.innerHTML = _.join(['Hello', 'webpack'], ' ');
  return element;
}
document.body.appendChild(component());
function component1() {
  var element = document.createElement('div');
  var gou = new Gou();
  element.innerHTML = gou.sayHello();
  return element;
}
document.body.appendChild(component1());
```

dist/index.html:
```
<!doctype html>
<html>
  <head>
    <meta charset=utf-8>
    <title>test</title>
  </head>
  <body>
    <script src="main.js"></script>
    <script src="bundle.js"></script>
  </body>
</html>
```

## 5.6 执行打包

$ npx webpack /* 打包 */

## 5.7 验证

用浏览器打开dist/index.html，显示2次hell webpack，1次helle, I am goudan。