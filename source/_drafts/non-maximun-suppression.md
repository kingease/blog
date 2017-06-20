---
title: non-maximun suppression
tags:
---


1. 先按照评分从高到低的进行排序
2. 依次检测bbox的后续的bbox和他的交叉面积jaccard的得分
3. 如果这个得分达到一定水平就将其抑制
4. 保留交叉较少的说明是新的对象


这给我一个思路，可不是用现有的方法提取感兴趣的区域，然后用最大值抑制找感兴趣的区域呢？


## reference

1. http://www.pyimagesearch.com/2014/11/17/non-maximum-suppression-object-detection-python/
2. http://blog.csdn.net/shuzfan/article/details/52711706