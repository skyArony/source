---
title: AI-自然语言处理(一)
date: 2018-01-24 15:32:36
tags: 
- AI
- 人工智能
- 自然语言处理
categories: 
- 人工智能
- 自然语言处理
thumbnail: 
---
还没入门，不知道怎么组织文章结构，先随便记一记。

# 一、文本相似度的计算
关于文本相似度的几个计算公式：
- 欧氏距离
- 余弦相似度
- Jaccard距离
- 编辑距离

## 1. 欧式距离
最常见的距离度量，衡量的是多维空间中两个点之间的绝对距离。

$$d(x,y) = \sqrt{\sum_{i=1}^N(x_i-y_i)^2}$$

## 2. 余弦相似度
测量两个向量的夹角的余弦值来度量它们之间的相似性。相比欧式距离，更注重方向上的差异性。0 度角余弦为 1，90 度角余弦为 0，180 度角，余弦为 -1。

**余弦定理**

$$cos\theta = \frac{a^2+b^2-c^2}{2ab}$$

**点积公式**

$$\vec a \cdot \vec b = |\vec a||\vec b|cos\theta$$

$$cos\theta = \frac{\vec a \cdot \vec b}{|\vec a||\vec b|}$$

**改写得到**

$$cos\theta = \frac{\sum_{i=1}^N x_i \times y_i}{\sqrt{\sum _{i=1}^N(x_i)^2}\times\sqrt{\sum _{i=1}^N(y_i)^2}}$$

## 3. Jaccard距离
Jaccard 相似指数用来度量两个集合之间的相似性，它被定义为两个集合交集的元素个数除以并集的元素个数。

$$J(A,B)=\frac{|A \cap B|}{|A \cup B|}$$

Jaccard 距离用来度量两个集合之间的差异性，它是 Jaccard 的相似系数的补集，被定义为 1 减去 Jaccard 相似系数。

$$d_J(A,B)=1-J(A,B)=\frac{|A \cup B|-|A \cap B|}{|A \cup B|}$$


## 4. 编辑距离
