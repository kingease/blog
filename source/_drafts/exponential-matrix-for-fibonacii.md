---
title: exponential matrix for fibonacii
mathjax: true
tags:
---

## 斐波那契数列的矩阵幂

## 幂乘的简化
任何幂乘都可以简化计算， 例如 $a^N$, 我们可以乘以$a$ 共 $N$次，
``` python
ret = 1
for _ in range(N):
    ret *= a
```

上述计算的复杂度假设为$O\left(N\right)$, 但是可以降到 $O\left(logN\right)$， 因为
$$ N = x_k \* 2^k + \cdots + x_1 \* 2 + x_0 $$

其中 $k = logN$。 我们只需要计算 $a^\left(2^i\right)$ 

``` python
def power_of(a, N):
    power = N
    ret = 1
    while power > 1:
        if (power % 2) == 0: 
            power = power // 2
        else:
            ret = a * ret
            power = (power - 1) // 2
        a = a * a

    # the last step, power%2 == 0
    ret = a * ret
    return ret
```

