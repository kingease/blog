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

对 datasets 的理解参考 data reading in tensorflow：
http://honggang.io/2016/08/19/tensorflow-data-reading/

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

#### 如何对网络中的部分变量进行恢复
https://github.com/tensorflow/tensorflow/tree/master/tensorflow/contrib/slim#partially-restoring-models

恢复的模型的中变量的名称要和生成的model.ckpt的模型中命名的变量名要一致。
几个关键的函数
```
slim.get_model_variables()
```
```
slim.assign_from_checkpoint_fn()
```

建立恢复映射
``` python
# 获得当前网络的所有变量名
my_model_variables = slim.get_model_variables()
origin_model_variables = [name_in_checkpoint(var) for var in my_model_variables]
# 建立映射
variables_to_store = { ori:var for ori, var in zip(origin_model_variables, my_model_variables)}
```
一般当前网络的命名和checkpoint的命名有一定的规律如 `vgg_16/conv1/weight` 在新的网络是`ssd_vgg_512/conv1/weight` 可以定义
``` python
def name_in_checkpoint(var):
    var.op.name.replece('sdd_vgg_512', 'vgg_16')
```

应用恢复映射
```
tf.train.Saver(variables_to_restore)
```
or 
```
slim.assign_from_checkpoint_fn(
        checkpoint_path,
        variables_to_restore)
```

#### 在恢复网络参数的过程中的依次选择加载文件的逻辑是：
train_dir > checkpoint_path(.ckpt) > checkpoint_path(latest)
1. 检查 train_dir 下是否有ckpt文件
    ``` python
    checkpoint_path = tf.train.latest_checkpoint(train_dir)
    ```
    如果有就加载到模型内，忽略后面的checkpoint_path。
2. 检查 checkpoint_path, 是否是文件
    如果是文件，据加载文件
3. 如果是目录，就加载 checkpoint_path 文件夹下最进的 checkpoint 文件


可共使用的模型列表：
https://github.com/tensorflow/models/tree/master/slim

### loss
``` python
loss = ...
tf.losses.add(loss)
```

## Evaluation的组件与流程

## Inference的组件与流程

## 参考

[官方样板工程][https://github.com/tensorflow/models/tree/master/slim](https://github.com/tensorflow/models/tree/master/slim)
[mnist工程][https://github.com/mnuke/tf-slim-mnist](https://github.com/mnuke/tf-slim-mnist)
[ssd tf工程][https://github.com/balancap/SSD-Tensorflow](https://github.com/balancap/SSD-Tensorflow)
[slim notebook][https://github.com/tensorflow/models/blob/master/slim/slim_walkthrough.ipynb](https://github.com/tensorflow/models/blob/master/slim/slim_walkthrough.ipynb)
[slim doc][https://github.com/tensorflow/tensorflow/tree/master/tensorflow/contrib/slim](https://github.com/tensorflow/tensorflow/tree/master/tensorflow/contrib/slim)
[slim model][https://github.com/tensorflow/models/tree/master/slim](https://github.com/tensorflow/models/tree/master/slim)