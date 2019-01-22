---
title: '机器学习基石-Lecture05: Training versus Testing'
date: 2018-01-16 01:20:12
tags: 
    - "Machine Learning"
    - "机器学习"
    - "NTU机器学习基石"
categories: 
    - "Machine Learning"
    - "NTU机器学习基石"
---
从上节课我们可以知道

![Lecture04](机器学习基石-Lecture05-Training-versus-Testing/Lecture04.png)

当样本数据足够大并且hypothesis数量有限时，ML是可以成立的

那么本节我们将讨论，为什么机器可以学习

(哇，这节要认真听第一遍不是那么特别好理解的)

![Lecture05](机器学习基石-Lecture05-Training-versus-Testing/Lecture05.png)


**Hoeffding's inequality是ML的理论基础，可以推导出机器学习在理论上的可行性**


<!--more-->

## Recap and Preview

回顾ML的flow图

![flow](机器学习基石-Lecture05-Training-versus-Testing/flow.png)

当样本数据足够大并且hypothesis数量有限时，能保证E_in_and_E_out PAC

而此时ML是可行的
![learning_possible](机器学习基石-Lecture05-Training-versus-Testing/learning_possible.png)

回顾前四节课我们所学到的知识

![Recap](机器学习基石-Lecture05-Training-versus-Testing/Recap.png)

1. Lecture1，ML的目标是找出合适的g≈f，即保证E_out(g)≈0
2. Lecture2，介绍PLA和pocket算法，演示如何E_in≈0
3. Lecture3，介绍了ML的分类情况，当前ML最基本的，监督式批量数据二元分类法
4. Lecture4，把E_in(g)，E_out(g)联系起来，得知在一定条件下会有E_in(g)≈E_out(g)

![central_questions](机器学习基石-Lecture05-Training-versus-Testing/central_questions.png)
所以总的看下来，ML的两个关键问题：
1. 如何使E_in(g)≈E_out(g)
2. 如何使E_in足够小

对这两个关键问题来说，hypothesis的数量M与他们有什么关系呢？

![M](机器学习基石-Lecture05-Training-versus-Testing/M.png)

可以想见  

当M很小时(hypothesis数量很少)，可以满足第一个问题，但是无法确定能找到最小的E_in

当M很大时(hypothesis数量很多)，可以满足第二个条件，但是又无法保证E_in_and_E_out PAC

所以可以知道，选择正确的M很重要

那么，当M是无穷的，会发生什么？

![infinite_M](机器学习基石-Lecture05-Training-versus-Testing/infinite_M.png)

为什么当选择无穷的M，PLA居然可以顺利的解决了？

我们会在接下来三节课中深入研究这个谜团


## Effective Number of Lines

尝试算出bad event的整个数量      
当把所有的bad_event情况做联集(union bound)时，会发现     
可能会导致无限大     
所以边界看起来会失效   
![bad_event](机器学习基石-Lecture05-Training-versus-Testing/bad_event.png)

不过，我们继续看bad_event情况    
会发现其实所有的bad_event彼此之间重叠的部分非常大(彼此相近)   
所以使用union bound会导致我们对bound过度的估计   
![over_estimating](机器学习基石-Lecture05-Training-versus-Testing/over_estimating.png)

那么，接下来重要的问题就是：如何找到bad_event彼此之间重叠的部分
(可以理解为，将无数个hypothesis分成有限个类别)


#### Effective Number of Lines examples

下面通过一系列举例来探索这个问题

1. 首先，平面中有1个点，可以使用直线将平面切割，对直线来说，平面上所有点的情况只有2种

![2kinds](机器学习基石-Lecture05-Training-versus-Testing/2kinds.png)

2. 当平面中有2个点，会有4种情况

![4kinds](机器学习基石-Lecture05-Training-versus-Testing/4kinds.png)

3. 当平面中有3个点，会有8种情况

![8kinds](机器学习基石-Lecture05-Training-versus-Testing/8kinds.png)

4. 再次思考3个点的情况，会有少于8中分割的情况(有点共线时)

![lewer8kinds](机器学习基石-Lecture05-Training-versus-Testing/lewer8kinds.png)

5. 当平面中有4个点，会有14种情况(有一种情况线性不可分)

![14kinds](机器学习基石-Lecture05-Training-versus-Testing/14kinds.png)

所以，我们引入Effective Number of Lines(有效线)

![EffectiveNumberofLines](机器学习基石-Lecture05-Training-versus-Testing/EffectiveNumberofLines.png)

#### Effective Number of Lines pattern

我们会发现有效线存在一定的规律

![pattern](机器学习基石-Lecture05-Training-versus-Testing/pattern.png)

1. 一定小于2^N
2. 通过分组可以使得这些分割线是有限的

最终推理出，学习可能的有效性

![possible](机器学习基石-Lecture05-Training-versus-Testing/possible.png)


## Effective Number of Hypotheses

一个新名词：二分类(dichotomy)

![dichotomy](机器学习基石-Lecture05-Training-versus-Testing/dichotomy.png)

dichotomy set可不可以取代掉M？

#### Growth_Function
进一步的

前例可以看出前面的dichotomy set依赖于输入的数据

那么如何去除这个限制呢？

![Growth_Function](机器学习基石-Lecture05-Training-versus-Testing/Growth_Function.png)

取得dichotomy最大的情况(Growth_Function),记为m_h(N)

也就是前面所提的Effective Number of Lines的最大值


#### 求得Growth_Function

1. Positive Rays的 growth func

对于一维直线来说，存在N个点对直线做切割，会存在N+1个部分

![PositiveRays](机器学习基石-Lecture05-Training-versus-Testing/PositiveRays.png)

可以知道m_h(N) = N + 1

即，通过m_h(N)取代M，会极大的减小M的值

2. Positive Intervals的 growth func

再看一种一维空间的情况

![PositiveIntervals](机器学习基石-Lecture05-Training-versus-Testing/PositiveIntervals.png)

我们依旧会得到m_h(N)极大的减小M的值

3. Convex Sets的 growth func

那么对二维空间   

定义一个hypothesis set是在空间的Convex(凸多边形)空间

![ConvexSets](机器学习基石-Lecture05-Training-versus-Testing/ConvexSets.png)

那么对于这个ConvexSets的growth func

可以使用多边形，将所有点连接起来

所以每次产生一个dichotomy都可以使用这种办法生成一个凸多边形

这样凸多边形内外就是分割平面的dichotomy

这样，所有的dichotomy都可以做出来

![shattered](机器学习基石-Lecture05-Training-versus-Testing/shattered.png)


## Break Point

到这里，已经介绍了4种Growth_Function

![4_Growth_Function](机器学习基石-Lecture05-Training-versus-Testing/4_Growth_Function.png)

positive rays和positive intervals的成长函数都是polynomial(多项式)的
那么这两种情况下，是可以使用Growth_Function替代M的

但是convex sets的成长函数是exponential(指数)的，如果使用这个m_h(N)替代M，依旧不能保证ML的可行性

那么，对于2D perceptrons的问题，它的成长函数究竟是什么呢？

#### Break Point

![Break_Point](机器学习基石-Lecture05-Training-versus-Testing/Break_Point.png)

引入新概念：Break Point

即最先使得m_k(h) < 2^K 的 那个数字

所以可以得到，上述4种Break_Point

![4_Break_Point](机器学习基石-Lecture05-Training-versus-Testing/4_Break_Point.png)

## Summary

![Summary](机器学习基石-Lecture05-Training-versus-Testing/Summary.png)
