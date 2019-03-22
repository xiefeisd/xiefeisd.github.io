---
layout:     post
title:      "微信小程序云函数的本地调试"
subtitle:   "wx-server-sdk包在本地的使用"
date:       2019-03-21
author:     "xiefeisd"
header-img: "img/post-bg.jpg"
tags:
    - 微信小程序
---

# 1 前提条件

微信小程序的云函数，是基于node.js的，所以需要先安装node.js、npm。

本文假设已全剧安装node.js、npm。

# 2 建立node.js工程项目

$ mkdir add /* 建立文件夹 */

$ npm init /* 初始化项目 */

# 3 给项目安装wx-server-sdk

$ npm install -save /* 局部安装 */

# 4 准备文件

index.js：

```javascript
const cloud = require('wx-server-sdk')
cloud.init()
exports.main = async (event, context) => {
  var sum = event.a + event.b
  return sum
}
```

test.js：

```javascript
var exports_mode = require('./index')
var event = { "a": 1, "b":2}
exports_mode.main(event,null)
```

# 5 文件结构

完成上述工作以后，项目结构如下：
```
.
├── index.js
├── node_modules
├── package.json
├── package-lock.json
└── test.js
```
其中：
- package.json 由命令`npm init`生成。 
- node_modules、package-lock.json 由`npm install -save`生成。
- indexjs 是云函数文件。
- test.js 用来调用 index.js，进行测试。

# 6 测试

$ node test.js /* 运行测试 */

可以观察到，输出为a、b之和。

修改test.js中的a、b的值，重新运行测试，可以观察到变化。

