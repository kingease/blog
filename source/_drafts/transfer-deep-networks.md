---
title: transfer deep networks
tags:
---

# 一个不错的的参考文章[利用Keras解释CNN的滤波器](http://keras-cn.readthedocs.io/en/latest/blog/cnn_see_world/)

文章的要点：
1. Keras 有 vgg16和vgg19 两个网络的权重（有下载地址）
2. 如何使用的 vgg 权重


## Keras如何使用vgg权重
1. 输入的图片大小可以不是vgg网络的224x224，需要舍去全连接层FC。

> 注意我们不需要全连接层，所以网络就定义到最后一个卷积层为止。使用全连接层会将输入大小限制为224×224，即ImageNet原图片的大小。这是因为如果输入的图片大小不是224×224，在从卷积过度到全链接时向量的长度与模型指定的长度不相符。



# tensorflow slim Library
[url]
https://github.com/tensorflow/models/tree/master/slim

[如何使用的jupyter notebook]
https://github.com/tensorflow/models/blob/master/slim/slim_walkthrough.ipynb


## TF-Slim
https://github.com/tensorflow/tensorflow/tree/master/tensorflow/contrib/slim
### tf>=1.0 可以 slim = tf.contrib.slim

TF-Slim is a lightweight library for defining, training and evaluating complex models in TensorFlow. 

如何使得创建、训练、评估网络变得简单：


### 创建网络

#### arg_scope的使用
https://github.com/tensorflow/tensorflow/tree/master/tensorflow/contrib/slim#scopes


### 使用pre-trained模型

### Fine-Tuning Existing Models

#### 在新的任务上使用现有的模型
假设我们已经有了一个pre-trained VGG16 模型。这个模型是训练在Image Net上有1000类上的模型。但我们想把它应用到Pascal VOC 数据集，但这个数据集只有20类。为了实现我们的目标，我们可以初始化我们的网络，使用pre-trained的模型，除了最后的一层。

VGG net
``` python
def vgg16(inputs):
  with slim.arg_scope([slim.conv2d, slim.fully_connected],
                      activation_fn=tf.nn.relu,
                      weights_initializer=tf.truncated_normal_initializer(0.0, 0.01),
                      weights_regularizer=slim.l2_regularizer(0.0005)):
    net = slim.repeat(inputs, 2, slim.conv2d, 64, [3, 3], scope='conv1')
    net = slim.max_pool2d(net, [2, 2], scope='pool1')
    net = slim.repeat(net, 2, slim.conv2d, 128, [3, 3], scope='conv2')
    net = slim.max_pool2d(net, [2, 2], scope='pool2')
    net = slim.repeat(net, 3, slim.conv2d, 256, [3, 3], scope='conv3')
    net = slim.max_pool2d(net, [2, 2], scope='pool3')
    net = slim.repeat(net, 3, slim.conv2d, 512, [3, 3], scope='conv4')
    net = slim.max_pool2d(net, [2, 2], scope='pool4')
    net = slim.repeat(net, 3, slim.conv2d, 512, [3, 3], scope='conv5')
    net = slim.max_pool2d(net, [2, 2], scope='pool5')
    net = slim.fully_connected(net, 4096, scope='fc6')
    net = slim.dropout(net, 0.5, scope='dropout6')
    net = slim.fully_connected(net, 4096, scope='fc7')
    net = slim.dropout(net, 0.5, scope='dropout7')
    net = slim.fully_connected(net, 1000, activation_fn=None, scope='fc8')
  return net
```

部分加载 权重`fc6` `fc7` `fc8` 重新训练.
``` python
# Load the Pascal VOC data
image, label = MyPascalVocDataLoader(...)
images, labels = tf.train.batch([image, label], batch_size=32)

# Create the model
predictions = vgg.vgg_16(images)

train_op = slim.learning.create_train_op(...)

# Specify where the Model, trained on ImageNet, was saved.
model_path = '/path/to/pre_trained_on_imagenet.checkpoint'

# Specify where the new model will live:
log_dir = '/path/to/my_pascal_model_dir/'

# Restore only the convolutional layers:
variables_to_restore = slim.get_variables_to_restore(exclude=['fc6', 'fc7', 'fc8'])
init_fn = assign_from_checkpoint_fn(model_path, variables_to_restore)

# Start training.
slim.learning.train(train_op, log_dir, init_fn=init_fn)
```

> 但最后一层怎么修改呢？

