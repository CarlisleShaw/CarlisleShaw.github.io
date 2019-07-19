---
layout:     post
title:      Why Is Dual-Pivot Quicksort Fast?
subtitle:   双基准快速排序为什么更快？不科学！
date:       2019-03-17
author:     Shaw
header-img: img/post-bg-altitude.jpg
catalog: true
tags:
---

## 简介
---
快速排序在1962年一经提出就一炮而红，各路人士纷纷前来研究快排，对原始的快排算法进行升级改进，快速排序得以迅猛发展。

如此一直发展到90年代，快速排序的实现方式基本固定下来，几乎所有的编程库中采用的都是现在大家熟知的经典快速排序算法。

变局发生在2009年，一个俄罗斯人提出了`Dual-Pivot Quicksort`（双基准快速排序），称这种新快排算法的表现**显著**好于`Java 6`中的经典快排算法。

谁信呢！快排在提出的几十年间都快被研究烂了，能改进的都改进了，近十年都没有新突破，怎么可能被突然冒出来的新算法打败！

然而，**真香**！在2011年，`Java 7`中采用了他的新算法取代了`Java 6`中的经典快排算法。

## 快速排序剖析
---
#### 经典快速排序算法

取一基数`B`，比`B`小的放左边，比`B`大的放右边。   

如此，数列被分成了左右两部分。   

接着对左右两部分分别递归操作。   

#### 双基数快速排序

取两基数，左基数`LB`和右基数`RB`，满足`LB`<=`RB`   

交换元素使数列满足如下条件：   
`Part I` <= `LB` <= `Part II` <= `RB` <= `Prat III`   

如此，数列被分成三部分。   

接着对三个部分分别递归操作。   

#### 比较时间复杂度

对于排序算法，我们通常用`N`个元素`比较多少次`来衡量时间复杂度。  

那么来看一下两种算法的平均比较次数：

|经典快速排序算法|  双基数快速排序|
|:--------------:|:--------------:|
|`1.5697N·ln(N)` | `1.7043N·ln(N)`|

Excuse Me ??    

不是说双基数表现比经典算法`显著`好嘛？   

从数据上看，双基数绝对比经典算法慢啊！   

咋肥四？

## The Memory Wall
---

在过去的25年中，CPU的速度平均每年增长`46%`，而内存的带宽每年只增长`37%`。

如此发展了25年，CPU与内存之间的速度差越来越大。

长此以往，在未来的某个时刻，CPU任何的性能提升都是徒劳的 —— 内存的速度成为了计算机性能的短板，CPU永远在等待内存传输数据而无法发挥出最大的性能。


## 破案
---

事实上，双基准快速排序算法相比经典快排算法，**CPU高速缓存命中率更高**，CPU和内存之间的I/O数据流量更少，CPU等待内存的时间减少，从而达到速度更快的效果。  


## 参考文献
---

为什么双基准快速排序缓存命中率更高？

怎么度量缓存命中率？（“扫描元素个数”）

深究这些问题可以参考如下几篇论文：

[Why Is Dual-Pivot Quicksort Fast?](https://www.researchgate.net/profile/Sebastian_Wild/publication/283532116_Why_Is_Dual-Pivot_Quicksort_Fast/links/5645af0708ae9f9c13e69f2c/Why-Is-Dual-Pivot-Quicksort-Fast.pdf?origin=publication_detail)  

[Dual-Pivot and Beyond: The Potential of Multiway Partitioning in Quicksort](https://www.wild-inter.net/publications/wild-2018b.pdf)  

[Analysis of Pivot Sampling in Dual-Pivot Quicksort](https://arxiv.org/pdf/1412.0193.pdf)  

[Discussion of Multi-Pivot Quicksort: Theory and Experiments](https://cs.stanford.edu/~rishig/courses/ref/l11a.pdf)  

