---
title: deep net structures
tags:
---


# deep net 的 组成
1. inputs
2. model_layers
3. loss
4. optimizer
5. graph
5. session

## Inputs
其实inputs 可以看成layer的一种特殊情况，但是由于它的地位很特殊，我们单独将它抽出来，单独讨论。TF中常使用的`tf.placeholders()`来定义输入层。

1.  图像
    1. 固定图片的长度、宽度和深度
        ``` 
        tf.placeholders(tf.float32, [None, width, height, channels]) 
        ```
2.  声音
    1. 
3.  文字
    1. 一般文字的处理方法 
       ```
       tf.placeholder(tf.int32, [batch_size, sequence_length])
       ```
       batch_size: None # 一般由输入的数据决定
       sequence_length: None # 一般由输入的数据决定


## ModelLayers
构成 graph 的基础构件，常用的 layer 的概念有：
2. dense/fully-connected layer (activation=None/RELU/sigmoid)
3. reshape layer
4. drop layer
4. conv2d layer
5. pooling layer
6. rnn layer
7. merge layer
8. embedded layer
9. encode layer
10. decode layer
11. activation layer
12. batch_normalization layer


### SubLayers
1. LSTM cell layer
2. Multi LSTM cells layer


### CombinedLayers
1. 卷积层
2. 输出层
3. 嵌入层
4. RNN层
5. 转换层 (convert to target num_class dim)


#### 输出层
和转换层一样，但是从意义上可以分开
```
tf.layers.dense(x, num_class, activation=None) 
```


#### RNN层 
```
# num_units:num_units是隐含层的维度（又称为num_hidden）
# 如果参数num_proj不设置，那么num_units也是输出数据的维度，即 cell.output_size，见下 outputs 结构。
cell = tf.contrib.rnn.BasicLSTMCell(num_units)

# num_layers: cell叠加的层数
stack = tf.contrib.rnn.MultiRNNCell([cell] * num_layers)

# 构成有输入输出的RNN层
outputs, _ = tf.nn.dynamic_rnn(
    cell(stack): SubLayer, # 较为固定
    sequence_length: Inputs, # RNN展开的次数 较为固定
    dtype: tf.float32, # 输出的类型

    inputs: ModelLayer/Inputs, # 通常来自嵌入层、图像展开层, 结构维度是[batch_size, max_time, num_features]
        batch_size(不解释)
        max_time(应该==sequence_length)
        num_feature(是输入特征的维数)
)
```

如果inputs的类型是Inputs，那么它的定义是这样的
```
tf.placeholder(tf.float32, [None, None, num_features])
# batch, num_step 由实际的传入的数据决定
```

output的数据结构是怎样的呢？
默认的是 [batch_size, max_time, cell.output_size]，其中，
    cell.output_size（== cell.num_proj if num_proj was set, num_units otherwise.）


#### 转换层
将输入的 tensor [batch_size, num_dim] 转换成指定维度的 logits [batch_size, num_class], 线性输出
```
tf.layers.dense(x, num_class, activation=None) 
```

> 如何将RNN输出的[batch_size, max_time, num_units] 转换成 [batch_size, max_time, num_classes]?

1. 先将 `t1 = tf.reshape(tensor, [-1, num_units])`
2. 再将 `t2 = tf.layers.dense(t1, num_classes, activation=None)`
3. 最后 `t3 = tf.reshape(t2, [batch_size, max_time, num_classes])`


## Loss
2. cross entrophy
1. ctc 

### ctc loss
目前看到的 ctc loss 的教程都来自 `https://github.com/igormq/ctc_tensorflow_example`。
```
loss = tf.nn.ctc_loss(
    labels: Inputs, # `tf.sparse_placeholder(tf.int32)`, # 目标标签的占位,固定不变
    sequence_length: Inputs, # `tf.placeholder(int32,[batch_size])`,  # batch_size=None,固定不变

    inputs: ModelLayer, # 通常是输出层给出的logits, 结构维度应该是[max_time, batch_size, num_classes]
        max_time（最大的序列，应该==sequence_length）
        num_classes（应该==(num_labels + 1), num_labels 为空标签（新增）保留。 ）
        num_labels（子标签的种类数量）
)
```

ctc loss 是解决序列中间添加空白标签和重复标签时，预测和目标之间的损失函数。 所以，它最终将问题转化为`多个` `多标签`的预测， 只是要预测足够多的steps, 然后在steps做合并组合，并计算损失。

logits的结构，给我们最大的启示是：我们需要生成`[max_time, num_classes]`个回归结果。 因为ctc loss 通常和RNN配合使用，所以，这里应该有`max_time == seq_len`。

> 如何生成[max_time, num_classes]个logits结果?

参考[RNN层](#RNN层)，[转换层](#转换层)


> 假设目标是一串字母和数字，如何生成labels的这样的输入数据呢？

1. 一个batch的数据初始看来是这样的
    ```
    labels = ['abcd','12ab.34',...,'y1234'] # len(labels) == batch_size
    ```

2. 将每个串映射成整数数组
    ```
    labels = [['a','b','c','d'],
              ['1','2','a','b','.','3', '4'],
              ...
              ['y','1','2','3','4']]
    ```
    通过label字典`{'a':0,'b':1,'c':2,'d':3,'1':4,'2':5,'.':6,'3':7,'4':8,'y':9}`将字符映射成整数tf.int32，那么labels就变成：
    ```
    labels =[[0, 1, 2, 3],
             [4, 5, 0, 1, 6, 7, 8],
             ...
             [9, 4, 5, 7, 8]]
    ```

3. 转换成单一数组和在batch中位置索引组合的方式
```
indices, values = [], []
for n, seq in enumerate(labels):
    indices.extend(zip([n]*len(seq), range(len(seq))))
    values.extend(seq)
```
values是将所有的值放到一个数组里`[0,1,2,3,4,5,0,1,6,7,8,...,9,4,5,7,8]`
indexes是将他在batch中对应的位置`[(0,0),(0,1),(0,2),...,(batch_size-1, 0),..,(batch_size-1,4)]`

使用下面的函数可直接得到
``` python
def sparse_tuple_from(sequences, dtype=np.int32):
    """Create a sparse representention of x.
    Args:
        sequences: a list of lists of type dtype where each element is a sequence
    Returns:
        A tuple with (indices, values, shape)
    """
    indices = []
    values = []

    for n, seq in enumerate(sequences):
        indices.extend(zip([n] * len(seq), xrange(len(seq))))
        values.extend(seq)

    indices = np.asarray(indices, dtype=np.int64)
    values = np.asarray(values, dtype=dtype)
    shape = np.asarray([len(sequences), np.asarray(indices).max(0)[1] + 1], dtype=np.int64)

    return indices, values, shape
```


## optimizer
1. tf.reduce_*
2. tf.optimizer


## Graph

graph 是将 `inputs`, `model`, `loss`, `optimizer` 组合在一起的组件。

```
graph = tf.Graph()
with graph.as_default():
    ... 
```


## Session

这个部分有一个重要的部分就是batch的生成。

同一个batch的数据结构维度都需要是一致。

> 如何构造Inputs定义的结构？

例如，将图像作为RNN输入
``` python
tf.placeholders(tf.float32, [batch_size, max_step, num_feature])
```
batch_size: None    # 批次数量的多少， 由输入的数量决定
max_step: None      # 与图像width正相关的一个值, 可以是图像的高度， 由输入的数量决定
num_feature: 固定值  # 可以是图像的高度， 固定，不可改变，输入的任何数据都不能改变

### 案例
> 如何生成 文字图像数据 和 文字标签数据 为 RNN 和 ctc-loss 生成输入数据？

1. 首先，按直觉生成一个图像加一个文字串。
2. 生成 一个batch的图像数组和文字串数组。
3. 将图像数组转换成

# train 和 infer

## train

## infer
