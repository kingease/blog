---
title: how to load tfrecord and batchlization
tags:
---


上一次讲了如何生成tfrecord文件，以及如何读取。例如：
``` python
# 'train' 是 split_name
# FLAGS.data_dir 是数据所在的目录
dataset = mnist.get_split('train', FLAGS.data_dir)
```

这次讲读取到训练前的batch化。流程图大致是这样的：
![flowchart-read-and-batch-tfrecord](http://oor53bfqy.bkt.clouddn.com/dataset_to_batches_train1.png)


data reading in tensorflow
http://honggang.io/2016/08/19/tensorflow-data-reading/


## sparse tensor load
1. https://stackoverflow.com/questions/36917807/how-to-load-sparse-data-with-tensorflow

2. http://planspace.org/20170427-sparse_tensors_and_tfrecords/
3. https://mathehacker.com/2017/01/29/skip-gram-in-matrix-and-tfrecord-io
4. https://www.tensorflow.org/programmers_guide/reading_data#reading_from_files



Sparse input data

SparseTensors don't play well with queues. If you use SparseTensors you have to decode the string records using tf.parse_example after batching (instead of using tf.parse_single_example before batching).

> 如何在batch之后，形成Sparse Tensor?
https://www.tensorflow.org/api_docs/python/tf/parse_example

https://stackoverflow.com/questions/40610115/tensorflow-use-tf-parse-example-for-jpeg-batches

## reference
1. https://saicoco.github.io/tf3/