---
layout:     post
title:      "矩阵相乘的几何意义"
subtitle:   "对矩阵相乘的形象化理解"
date:       2019-03-18
author:     "xiefeisd"
header-img: "img/post-bg.jpg"
tags:
    - 数学
    - 线性代数
    - 矩阵
---

# 1 向量相乘

令：

$$\vec {a} = \begin{bmatrix} x1&y1\\ \end{bmatrix} $$

$$\vec {b} = \begin{bmatrix} x2&y2\\ \end{bmatrix} $$

有：

$$\vec {a} * \vec {b} = x1 * x2 + y1 * y2 $$

几何意义：

$$\vec {a} * \vec {b} = |a|*|b|*cos(\theta)$$

$$\theta$$是$$\vec {a}$$、$$\vec {b}$$的夹角。

$$\vec {a} * \vec {b}$$是$$\vec {a}$$在$$\vec {b}$$方向上的投影长度与$$\vec {b}$$的模的乘积。

# 2 矩阵相乘

令：

$$ A = \begin{bmatrix} a_{0,0}&a_{0,1}\\a_{1,0}&a_{1,1} \end{bmatrix} $$

$$ B = \begin{bmatrix} b_{0,0}&b_{0,1}\\b_{1,0}&b_{1,1} \end{bmatrix} $$


再令：

$$\vec {ar_{0}} = \begin{bmatrix} a_{0,0}&a_{0,1} \end{bmatrix} $$

$$\vec {ar_{1}} = \begin{bmatrix} a_{1,0}&a_{1,1} \end{bmatrix} $$

$$\vec {bc_{0}} = \begin{bmatrix} b_{0,0}\\b_{1,0} \end{bmatrix} $$

$$\vec {bc_{1}} = \begin{bmatrix} b_{0,1}\\b_{1,1} \end{bmatrix} $$

有：

$$ A = \begin{bmatrix} \vec {ar_{0}}\\\vec {ar_{1}} \end{bmatrix} $$

$$ B = \begin{bmatrix} \vec {bc_{0}}&\vec {bc_{1}} \end{bmatrix} $$

有：

$$ A * B = \begin{bmatrix} \vec {ar_{0}} * \vec {bc_{0}} & \vec {ar_{0}} * \vec {bc_{1}} \\ 
           \vec {ar_{1}} * \vec {bc_{0}} & \vec {ar_{1}} * \vec {bc_{1}} \end{bmatrix} $$

几何意义：

将矩阵B的列向量依次向矩阵A的行向量进行“投影”。

# 3 投影是什么鬼

观察一个现实问题：

有一家商店，出售M种商品，获得了N天的销售纪录，请统计销售情况。

为了简化问题，令M=2，N=2，有：

单价表 | 第1天 | 第2天
----|----|----
商品1 | 1 | 2
商品2 | 3 | 4

销量表 | 商品1 | 商品2
----|----|----
第1天 | 5 | 6
第2天 | 7 | 8

用矩阵表示2张数据表：

单价表：

$$ A = \begin{bmatrix} 1&2\\3&4 \end{bmatrix} $$

销量表：

$$ B = \begin{bmatrix} 5&6\\7&8 \end{bmatrix} $$

令：

$$\vec {ar_{0}} = \begin{bmatrix} 1&2 \end{bmatrix} $$

$$\vec {ar_{1}} = \begin{bmatrix} 3&4 \end{bmatrix} $$

$$\vec {bc_{0}} = \begin{bmatrix} 5\\7 \end{bmatrix} $$

$$\vec {bc_{1}} = \begin{bmatrix} 6\\8 \end{bmatrix} $$

有：

$$ A * B = \begin{bmatrix} \vec {ar_{0}} * \vec {bc_{0}} & \vec {ar_{0}} * \vec {bc_{1}} \\ 
           \vec {ar_{1}} * \vec {bc_{0}} & \vec {ar_{1}} * \vec {bc_{1}} \end{bmatrix} $$

其中：

$$ \vec {ar_{0}} * \vec {bc_{0}} = [1,2] * [5,7] = 1 * 5 + 2 * 7 $$

现实意义：

“投影”就是两组数字取乘积之和，实现一种统计算法。

对了，这个例子是一个价格上涨导致销售量上涨的悲伤的故事。

# 4 矩阵相乘的代数学意义

观察一个方程组：

$$\begin{cases} a1 * x + b2 * y = c1 \\ a2 * x + b2 * y = c2 \end{cases}$$

写成矩阵的形式：

$$ \begin{bmatrix} a1&b1 \\ a2&b2 \end{bmatrix} * \begin{bmatrix} x \\ y \end{bmatrix} = \begin{bmatrix} c1 \\ c2 \end{bmatrix} $$

启示：

矩阵是方程组的另一种表达形式。

