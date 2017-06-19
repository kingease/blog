---
title: tensorflow slim tutorial
tags:
---

## slim 的工程的结构
1. datasets
2. preprocessing
3. nets
4. deployment
5. checkpoints

### 1. datasets
训练时，读取使用的数据集，分片等，所有的数据集都要封装成module。

### 2. preprocessing
图像的预处理函数 不同的网络，可能有不同的预处理函数，如：
1. vgg
2. lenet
3. ssd_vgg

### 3. nets
1. 存放网络结构,网络结构中不包含`INPUTS`层的定义，但默认好作为输入。
2. nets有istraining开关，决定是否使用在训练还是推断阶段。

### 4. deployment
暂时不知道，可能跟部署有关

### 5. checkpoints
存放训练的保存的模型

## Train的组件与流程
进行基本训练的流程图如下
![train flow chart](http://oor53bfqy.bkt.clouddn.com/slim_flowchart.png)

### create datasets
1. 直接生成数据
    生成numpy数据，（在后面转成tf的tensor）例如
    ``` python
    def produce_batch(batch_size, noise=0.3):
    xs = np.random.random(size=[batch_size, 1]) * 10
    ys = np.sin(xs) + 5 + np.random.normal(size=[batch_size, 1], scale=noise)
    return [xs.astype(np.float32), ys.astype(np.float32)]

    x_train, y_train = produce_batch(200)
    ```
2. 使用datasets moudle
    参考 How to convert dataset to TF-Record files and read TF-Record files
    ```
    dataset = mnist.get_split('train', FLAGS.data_dir)
    ```

    [如何查看dataset中的图像数据]

### wrap data
1. 将生成的转成tf的tensor, 例如
    ``` python
        def convert_data_to_tensors(x, y):
        inputs = tf.constant(x)
        inputs.set_shape([None, 1])
        
        outputs = tf.constant(y)
        outputs.set_shape([None, 1])
        return inputs, outputs
    ```
2. 分片划数据 batchilization
    参考 how to load tfrecord and batchlization，大致流程是
    ![batchilization](http://oor53bfqy.bkt.clouddn.com/dataset_to_batches_train1.png)

### nets
创建网络, 只考虑前向部分的网络。一般需要 istraining 开关。
对于图像，一般有 `images` tensor 作为输入。

[如何创建网络和打印网络结构]

### loss
``` python
loss = ...
tf.losses.add(loss)
```

## Evaluation的组件与流程

## Inference的组件与流程

## 参考
[slim doc](https://github.com/tensorflow/tensorflow/tree/master/tensorflow/contrib/slim)
[官方样板工程](https://github.com/tensorflow/models/tree/master/slim)
[mnist工程](https://github.com/mnuke/tf-slim-mnist)
[ssd tf工程](https://github.com/balancap/SSD-Tensorflow)
[slim notebook](https://github.com/tensorflow/models/blob/master/slim/slim_walkthrough.ipynb)