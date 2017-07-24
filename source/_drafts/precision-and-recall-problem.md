---
title: precision and recall problem
tags:
---

我们通常会遇见以类问题： precison/recall 问题

比如：我们想对图像中的一组 bounding box 进行分组， 希望靠的比较近的分在一起


这样就会有这样目标，我们希望找到一个盒子
1. 能装的bounding box 的个数尽量多（recall)
2. 我们希望这个盒子能够尽量的小

这就形成了 precision/recall tradeoff


