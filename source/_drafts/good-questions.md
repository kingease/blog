---
title: 问题集
tags:
---


## 概率
1. 已知一个经验分布，我们如何通过这个分布来产生随机数？                                                                                          
```python
def pick_word(probabilities, int_to_vocab):

    t = np.cumsum(probabilities)
    rand_s = np.sum(probabilities) * np.random.rand(1)
    pred_word = int_to_vocab[int(np.searchsorted(t, rand_s))]

    return pred_word
```


已知 x 服从一个分布，那么 y = CDF(x) 服从均匀分布。 
x = ICDF(y), y 由 均布分布生成随机数。


2. 在对朗伯体的三维测量，如何获得物体的深度图像？

max(0, n·l_i) = I_i
\min \sum(n·l_i - I_i)^2


## 识别

1. 什么是生成模型(generative)？ 什么是判别模型（discriminative)?

生成模型是为每一个类别 构建（分布）模型， 然后，新进来的数据更符合那个模型的过程。
判别模型，是在特征空间中建立判别面。


2. 什么是boosting 方法？

boosting 是一组弱分类器的组合。

弱分类器错分的样本，会被增加权重。再进行重新训练，生成新的弱分类器。
再将这些分类器线性组合在一起。

