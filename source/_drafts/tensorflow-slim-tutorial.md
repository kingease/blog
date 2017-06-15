---
title: tensorflow slim tutorial
tags:
---

## 参考
[slim doc](https://github.com/tensorflow/tensorflow/tree/master/tensorflow/contrib/slim)

[官方样板工程](https://github.com/tensorflow/models/tree/master/slim)
[mnist工程](https://github.com/mnuke/tf-slim-mnist)
[ssd tf工程](https://github.com/balancap/SSD-Tensorflow)

## slim 的工程的结构
1. datasets
2. preprocessing
3. nets
4. deployment
5. checkpoints

### 1. datasets
训练时，读取使用的数据集，分片等，所有的数据集都要封装成module。

#### 使用现成的数据集
这肯定不是我们需要的，毕竟我们要提供自己的数据集，或者将数据集转换成，这种格式。不过它介绍大致的流程

1. 下载并转换成TF-Record的格式
``` bash
$ DATA_DIR=/tmp/data/flowers
$ python download_and_convert_data.py \
    --dataset_name=flowers \
    --dataset_dir="${DATA_DIR}"
```
文件夹的结构是
``` bash
$ ls ${DATA_DIR}
flowers_train-00000-of-00005.tfrecord
...
flowers_train-00004-of-00005.tfrecord
flowers_validation-00000-of-00005.tfrecord
...
flowers_validation-00004-of-00005.tfrecord
labels.txt
```

2. 在datasets的package(文件夹)下建立flower数据集module
``` python
# https://github.com/tensorflow/models/blob/master/slim/datasets/flowers.py

def get_split(split_name, dataset_dir, file_pattern=None, reader=None):
  """Gets a dataset tuple with instructions for reading flowers.
  Args:
    split_name: A train/validation split name.
    dataset_dir: The base directory of the dataset sources.
    file_pattern: The file pattern to use when matching the dataset sources.
      It is assumed that the pattern contains a '%s' string so that the split
      name can be inserted.
    reader: The TensorFlow reader type.
  Returns:
    A `Dataset` namedtuple.
  Raises:
    ValueError: if `split_name` is not a valid train/validation split.
  """
  ...
```


#### 自己转换格式
参考：https://kwotsin.github.io/tech/2017/01/29/tfrecords.html


### 2. preprocessing
图像的预处理函数 不同的网络，可能有不同的预处理函数，如：
1. vgg
2. lenet
3. ssd_vgg

### 3. nets
存放网络结构

### 4. deployment
暂时不知道，可能跟部署有关

### 5. checkpoints
存放训练的保存的模型