---
layout:     post
title:      "狗蛋与babel的初遭遇"
subtitle:   "新手第一次使用babel的曲折过程"
date:       2019-03-16
author:     "xiefeisd"
header-img: "img/post-bg.jpg"
tags:
    - 工具使用
    - babel
---

# 0 前言 懵逼的狗蛋

从前，有个狗蛋，好吧，那个狗蛋就是我。

有一天，狗蛋用WebGL写了一段动画，在chrome、firefox跑的可溜了。

当狗蛋测试IE的时候，IE明晃晃的提示，某行有错误，而那行只有一句话，class Vector2{。

原因是，IE不支持ES6。

懵逼，大写的懵逼，早知道我就直接写ES5好了。

我特么又不是专业的程序员，我不在乎什么ES5、ES6，我只要迅速表达我的想法。

可是已经写好的几千行代码，手动改回来是不可能了。

上个周手动从ES5改到ES6的，那时候代码量还不大，从来只知道升级，鬼知道js还需要降级。

没办法，babel学起来。

# 1 安装node.js和npm

babel运行在node.js上，它由npm（node package manager）管理，所以安装babel之前需要先安装node.js和npm。

$ sudo apt-get install nodejs /* 安装nodejs */

$ sudo apt-get install npm /* 安装npm */

最新的node是10.15，apt-get安装的是8.10，狗蛋决定先这么用了。

# 2 使用babel

## 2.1 全局还是本地

babel有两种安装方法，全局安装、本地安装。

全局安装指的是，一次安装，到处使用。

本地安装指的是，只对当前项目有效，穿上裤子出了门就不认识了，灵活性比较高。

综上，狗蛋决定要本地安装，在安装之前需要先建立项目。

## 2.2 什么是项目

所谓建立项目，就是建一堆文件夹。

狗蛋以前是干工程（建筑）的，感觉这跟干工程一样一样的。

老板说搞个项目，狗蛋就用彩钢板弄一堆临建，里边有没有人办公先不用管。

## 2.3 搞起来

### 2.3.1 建立项目

$ mkdir goudan /* 建立根目录 */

$ cd goudan /* 进入根目录 */

$ mkdir src /* 建立源代码目录 */

$ mkdir dist /* 建立发布目录 */

$ npm init /* 初始化项目 */

需要填写一堆信息，不知道怎么填的就回车。

这也跟狗蛋干工程一毛一样的，有没有人干活先不管，给公司、财务、业主、监理的报备材料是要有的。

$ ls /* 看看npm init干了什么，原来丫的建了个package.json */

$ vim package.json /* 看看package.json里都有啥，就是刚才填的那些信息，果然就是一张报备表 */

$ :q /* 看一眼就好，看一眼就走，跟狗蛋巡视项目现场一毛一样的 */

### 2.3.2 安装babel

$ npm install --save-dev babel-cli /* 安装babel，主角终于来了 */

$ ls /* 看看babel干了啥，它建了个目录叫node_modules，另外在package.json里添加了点东西 */

这跟施工队进场一毛一样，带来了一堆施工设备，相应的往报备表里填写信息。

$ ./node_modules/.bin/babel --version /* 查看babel的版本 */

$ npm install babel-preset-env --save-dev /* 安装babel-preset-env */

babel-preset-env的功能是把ES6转成ES5，babel只是个包工头，babel-preset-env才是干活的老师傅。

$ ls /* 看看babel-preset-env干了嘛，好像啥也没干，它住在包工头的宿舍里，但他在报备表里有一行信息 */

# 3 干活

babel的功能是把ES6转成ES5，官方的说法可能不大一样。

这也跟干工程一毛一样的，投标方说的天花乱坠，但是狗蛋经理就只想让它干眼前这个low逼的活。

## 3.1 设置工作条件

$ vim .babelrc /* 建立.babelrc文件，内容如下 */

{
  "presets": ["env"]
}

狗蛋经理用这个文件告诉包工头babel，今天的活指定env老师傅出场，env是babel-preset-env的昵称。

## 3.2 准备原材料

狗蛋经理需要自己准备ES6，作为babel工作的原材料。

$ vim src/goudan.js /* 建立源代码文件，内容如下 */

```javascript
class Gou{
    constructor(){
        this.name = "goudan";
    };
    sayHello(){
        return "Hello, I am goudan.";
    };
}
```

## 3.3 干活

$ ./node_modules/.bin/babel src --outdir dist /* 下达作业指令，把src里的材料加工好放到dist */

$ ls dist /* 看一眼工作成果，确实有个文件被生成了 */

## 3.4 验证工作成果

$ vim index.html /* 创建一个html文件，内容如下 */

```
<!DOCTYPE html>
<html>
    <head>
<meta charset=utf-8>
    </head>
    <body> 
        <script src="./dist/goudan.js?v=3"></script>
        <script>
            var gou = new Gou();
            console.log(gou.sayHello());
        </script>
    </body>
</html>
```

用IE打开index.html，按F12，到控制台查看输出结果，与预期一致，说明工作成果有效。