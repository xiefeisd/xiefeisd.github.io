---
layout:     post
title:      "在ubuntu上使用jekyll"
subtitle:   "ubuntu jekyll ruby gem liquid"
date:       2019-03-16
author:     "xiefeisd"
header-img: "img/post-bg.jpg"
tags:
    - 工具使用
---

年纪大了，记性不好，把操作过程记下来备忘。

# 0 概念

ubuntu 是一种 Linux 操作系统。

ruby 是一种 面向对象的脚本语言。

gem 是 ruby 的包管理器。

liquid 是一种用于web开发的模板语言。

jekyll 是一种静态博客生成系统，github 使用它。

# 1 安装

$ sudo apt-get install ruby-full build-essential zlib1g-dev /* 安装依赖包

/* 设置环境变量 */
$ echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
$ echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
$ echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
$ source ~/.bashrc

$ gem install jekyll bundler /* 安装jekyll */

# 2 使用

$ jekyll new /* 新建站点 */

$ jekyll new --blank /* 新建空白站点 */

$ jekyll build /* 建立站点 */

$ jekyll serve /* 开启服务 */

$ jekyll doctor /* 诊断 */

$ jekyll new-theme /* 新建主题 */

$ jekyll clean /* 移除站点和meta数据 */

$ jekyll help /* 帮助 */

每次使用 jekyll serve 命令，都会重新编译站点，然后开启服务，服务端口为4000。

# 3 站点定制

_config.yml 文件储存了主要的定制信息。

可以clone别人的博客或模板，在其基础上进行修改。

# 4 使用github pages

首先建立 username.github.io 仓库，把本地站点push上去即可。

仓库的建立过程有坑，见另文。

# 5 了解 liquid 语言

liquid 语言用 `{ }` 进行标识，`{{ var }}` 里是变量，`{ conment }` 是注释。

内容是一些简单的逻辑判断，有其他语言基础不难理解。

至于自己写liquid，em，看我那天心情好吧。