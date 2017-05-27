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