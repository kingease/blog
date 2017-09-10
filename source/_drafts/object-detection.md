---
title: object detection
date: 2017-08-08 09:33:53
tags:
---


基于region proposal 的深度学习检测算法



关键词

Region Proposal：
1. select search / edge box
2. e2e 使用卷积神经网络直接产生。


1. 公用CNN层
2. 多任务损失函数
3. 金字塔采样


YOLO:
回归的方法
既给定输入图像，直接在图像的多个位置上回归出这个位置的目标边框以及目标类别。
7x7 粗糙网络 回归(位置 + 置信度 + 类别)


SSD：
是的RPN + 回归的方法


reference:
https://wenku.baidu.com/view/2ff82194fe4733687f21aac6.html


