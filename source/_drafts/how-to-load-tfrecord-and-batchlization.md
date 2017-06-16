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
![flowchart-read-and-batch-tfrecord](http://oor53bfqy.bkt.clouddn.com/dataset_to_batches_train.png)