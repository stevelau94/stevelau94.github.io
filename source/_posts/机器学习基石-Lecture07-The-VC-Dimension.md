---
title: '机器学习基石-Lecture07: The VC Dimension'
date: 2018-01-27 20:39:46
tags: 
    - "Machine Learning"
    - "机器学习"
    - "NTU机器学习基石"
categories: 
    - "Machine Learning"
    - "NTU机器学习基石"
---
上节课介绍了break point  

并且得出结论说只要break point存在，则M有上界，一定存在 E_out ≈ E_in

![Lecture06](机器学习基石-Lecture07-The-VC-Dimension/Lecture06.png)

这节课介绍ML领域一个特别重要的概念，VC Dimension

![Lecture07](机器学习基石-Lecture07-The-VC-Dimension/Lecture07.png)

<!--more-->

## Definition of VC Dimension

由之前的课程我们知道，一个H，如果它有break point k的话，那它的growth func就是有界的。上界称为Bound function
![Bound_function](机器学习基石-Lecture07-The-VC-Dimension/Bound_function.png)

而Bound function也是有界的，且上界为N^(k-1)

从数据上看，N(k−1)比B(N,k)松弛很多

![loosely](机器学习基石-Lecture07-The-VC-Dimension/loosely.png)

VC bound就可以转换为:

![VCbound](机器学习基石-Lecture07-The-VC-Dimension/VCbound.png)

有结论:
1. 有break point k\
2. N 足够大
3. E_out ≈ E_in
4. 选择一个矩g，使E_in ≈ 0，即能够学习

### VC Dimension

就是最多能够分类的个数，也可以说shatter最多的inputs个数

![VC_Dimension](机器学习基石-Lecture07-The-VC-Dimension/VC_Dimension.png)

四种情况的VC Dimension

![4_VC_Dimension](机器学习基石-Lecture07-The-VC-Dimension/4_VC_Dimension.png)

d_vc 代替 k，那么 VC bound 的问题也就转换为与 d_vc 和 N 相关了

![VC_Dimension_learning](机器学习基石-Lecture07-The-VC-Dimension/VC_Dimension_learning.png)

## VC Dimension of Perceptrons

这部分主要介绍d_vc = d+1

## Physical Intuition of VC Dimension

VD dimension 可以被当成是 H 的分类能力。

如图中W表示自由度

![degree_of_freedom](机器学习基石-Lecture07-The-VC-Dimension/degree_of_freedom.png)

M 和 d_vc 的关系

![M](机器学习基石-Lecture07-The-VC-Dimension/M.png)


## Interpreting VC Dimension

深入地探讨VC Dimension的意义

重新推导

![Rephrase](机器学习基石-Lecture07-The-VC-Dimension/Rephrase.png)

ϵ表现了假设空间H的泛化能力，ϵ越小，泛化能力越大。

推导出泛化误差 E_out 的边界

![Message](机器学习基石-Lecture07-The-VC-Dimension/Message.png)


样本复杂度（Sample Complexity）

![Sample_Complexity](机器学习基石-Lecture07-The-VC-Dimension/Sample_Complexity.png)

从实践来看，N ≈ 10 d_VC 就OK了

10倍的d_VC

VC Bound基本上对所有模型的宽松程度是基本一致的，所以，不同模型之间还是可以横向比较。

![Looseness](机器学习基石-Lecture07-The-VC-Dimension/Looseness.png)


VC Bound宽松对ML的可行性还是没有太大影响

