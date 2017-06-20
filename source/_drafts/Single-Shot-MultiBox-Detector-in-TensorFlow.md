---
title: Single Shot MultiBox Detector in TensorFlow
tags:
---


# 目标
- [ ] 看懂网络结构
    - [ ]  vgg
    - [ ]  feature layers
        - [ ] anchor 的 选择
            - [x] size
            - [x] ratio
    - [ ]  predictions
        - [x] 根据 size ratio 输出 default box
        - [ ] 输出 multi box
        - [ ]  nms
- [ ] 看懂代价方程
    - [ ] loss function: ssd_loss
        - [ ] anchors
        - [ ] encode
        - [ ] decode
- [ ] 看懂预处理步骤
    - [ ] tf_image_whitened
- [ ] 如何进行的数据的输入
    - [ ] slim的使用
    - [ ] dataset and batch `DatasetDataProvider` for train
    - [ ] single image `tf.placeholder`
- [ ] 如何检测一步一步的结果
    - [ ] tf.InteractiveSession
    - [ ] test 的方式 tf.test.TestCase
    - [ ] 查看datasets的数据


# SSD 的原理

使用前向卷积神经网络（feed-forward convolutional network）产生固定长度的一组包围盒（bounding box）,以及这些盒中物体类别（object class）,最后用非极值抑制（non-maximun suppression）产生最后的结果。

事实上做的是， 对默认的anchor_box (default box) 的和感兴趣区域的offset的回归。
并不是对每一个像素点的回归。
可以想象成feature_map所生成的一组boxes，从中挑出接近感兴趣区域的，然后对偏移量进行回归。

## prediction step 网络的创建
Multi-scale feature maps for detection 
基于多尺度特征的检测
在基础网络vgg之上，增加特征层的卷积。这些层逐渐减少尺寸，这样在不同尺度下，可以进行对象的预测。

Convolutional predictors for detection
对每一个增加的特征层可产生一组预测。

主要在`ssd_vgg_300.ssd_net()`内

几个重要的概念：
default boxes 就是 anchor boxes
    - anchor 就是 a given location （k 个）
    - location 由 size 和 ratio 组成 （k = len(size) + len(ratio)）
    - size 和 ratio 
    - 根据 size 和 ratio 进行 anchor box 的计算的函数在`ssd_vgg_300.ssd_anchor_one_layer`内。

feature layer: m x n x p(channel)

small kernel: 3x3xp

prediction output value: m x n
    1. score for category
    2. offset 相对于 default box coordinates.
        > 这一点还不是太清楚，是相对于每一个anchor box的偏移量吗？
        > 通过偏移量， 如何预测出 boundingbox？
        > 这里的offset 就是 上述的 location


> 如何根据对 default box 的 offsetes， 计算的bbox?

np_methods.ssd_bboxes_decode()

> 从一堆bboxes中挑选最有可能性的box的 non-maximun selection

np_methods.bboxes_nms()

### 网络结构
`block1 ~ block11` 描述 conv net 的结构

#### vgg
`block1 ~ block5` 
1. conv2d_rep
    - 2个2rep depth:[64, 128]， 
    - 3个3rep depth:[256, 512, 512]
    - block的名字由conv2d组决定
2. max_pool2d
    - 在每个conv2d_rep组后做一个
    - 5个max_pool2d

#### features layers


### 预测和定位层
`ssd_vgg_300.ssd_multibox_layer()`

``` python
predictions = []
logits = []
localisations = []
for i, layer in enumerate(feat_layers):
    with tf.variable_scope(layer + '_box'):
        p, l = ssd_multibox_layer(end_points[layer],
                                  num_classes,
                                  anchor_sizes[i],
                                  anchor_ratios[i],
                                  normalizations[i])
    predictions.append(prediction_fn(p))
    logits.append(p)
    localisations.append(l)
```

根据预测的 size(又称为scale)和ratio 恢复 anchor box 函数。可以视 预测的size和ratio 是anchor box的一种压缩。

### 为每一个feature map生成默认的anchor boxes 也就是default boxes
每一个feature map 的 每一个 方形的像素（或feature） 都有几个默认的anchor boxes
``` python
def ssd_anchor_one_layer(img_shape,
                         feat_shape,
                         sizes,
                         ratios,
                         step,
                         offset=0.5,
                         dtype=np.float32):
    """Computer SSD default anchor boxes for one feature layer.

    Determine the relative position grid of the centers, and the relative
    width and height.

    Arguments:
      feat_shape: Feature shape, used for computing relative position grids;
      size: Absolute reference sizes;
      ratios: Ratios to use on these features;
      img_shape: Image shape, used for computing height, width relatively to the former;
      offset: Grid offset.

    Return:
      y, x, h, w: Relative x and y grids, and height and width.
    """
    # Compute the position grid: simple way.
    # y, x = np.mgrid[0:feat_shape[0], 0:feat_shape[1]]
    # y = (y.astype(dtype) + offset) / feat_shape[0]
    # x = (x.astype(dtype) + offset) / feat_shape[1]
    # Weird SSD-Caffe computation using steps values...
    y, x = np.mgrid[0:feat_shape[0], 0:feat_shape[1]]
    y = (y.astype(dtype) + offset) * step / img_shape[0]
    x = (x.astype(dtype) + offset) * step / img_shape[1]

    # Expand dims to support easy broadcasting.
    y = np.expand_dims(y, axis=-1)
    x = np.expand_dims(x, axis=-1)

    # Compute relative height and width.
    # Tries to follow the original implementation of SSD for the order.
    num_anchors = len(sizes) + len(ratios)
    h = np.zeros((num_anchors, ), dtype=dtype)
    w = np.zeros((num_anchors, ), dtype=dtype)
    # Add first anchor boxes with ratio=1.
    h[0] = sizes[0] / img_shape[0]
    w[0] = sizes[0] / img_shape[1]
    di = 1
    if len(sizes) > 1:
        h[1] = math.sqrt(sizes[0] * sizes[1]) / img_shape[0]
        w[1] = math.sqrt(sizes[0] * sizes[1]) / img_shape[1]
        di += 1
    for i, r in enumerate(ratios):
        h[i+di] = sizes[0] / img_shape[0] / math.sqrt(r)
        w[i+di] = sizes[0] / img_shape[1] * math.sqrt(r)
    return y, x, h, w
```

feature map 的 anchor box 的中心是，
$$\left( \frac{i+0.5}{|f_k|}, \frac{j+0.5}{|f_k|} \right) $$
代码是
``` python
y, x = np.mgrid[0:feat_shape[0], 0:feat_shape[1]]
y = (y.astype(dtype) + offset) / feat_shape[0]
x = (x.astype(dtype) + offset) / feat_shape[1]
```
$|f_k|$ 是 feature map 的 feat_shape



### scale 和 ratio 如何选择 
对 512的大小 图像 如下：
layer = ['e1'  'e2' 'e3'  'e4'  'e5'  'e6' 'e7']
shape = [ 64    32   16     8    4     2    1  ]
size  = [0.04, 0.10, 0.26, 0.42, 0.58, 0.74, 0.9, 1.06]
ds =       [0.06, 0.16, 0.16, 0.16, 0.16, 0.16, 0.16]

一个 scale 对应多个 ratio. 
{1, 2 , 3, 1/2, 1/3}

## train step 代价方程
这个方法被称为[MultiBox method](https://arxiv.org/pdf/1312.2249.pdf)

在训练的时候，我们进行`预测 <-> 真值`的双边匹配（bipartite matching）。例如，如果我们预测了$N$个boxes, 真值有$M$个boxes, 那么就有$N\times M$个匹配的（原子）度量(loss)。

At training time, we first match these default boxes to the ground truth boxes. For example, we have matched two default boxes with the cat and one with the dog, which are treated as positives and the rest as negatives. The model loss is a weighted sum between localization loss (e.g. Smooth L1 [6]) and confidence loss (e.g. Softmax).

代价loss 分成两个部分
1. 位置代价 localization loss
对象box和预测的最接近的box之间的相似性度量。
2. 可信度代价 confendence loss
候选区域是否目标对象的 logistic loss

### 位置loss of loc
原子度量是
$$dist(l_i, g_j)$$
$l_i$ 是 第 i 个 预测的box的位置， $g_j$ 是 第 j 个真值box的位置。

全部的loss是 $F_{loc}$
$$ F(x,l,g) = \sum_{i, j} dist(l_i, g_j)$$

思想是将ground truth bbox 投射到 feature map 上的 default anchor box 上， 在和 gbbox 相交到一定程度的 deabox *信息*被写到 feature map(m x n x p) 的 ground truth location map( m x n x num_anchor) 中, 这些*信息*是以相对于anchorbox的偏移量形式保存。 


### 将 ground truth 的 bbox 转成每一个 feature map 的 anchor box 表示
主要代码在`ssd_common.tf_ssd_bboxes_encode_layer`内

``` python
def tf_ssd_bboxes_encode_layer(labels,
                               bboxes,
                               anchors_layer,
                               num_classes,
                               no_annotation_label,
                               ignore_threshold=0.5,
                               prior_scaling=[0.1, 0.1, 0.2, 0.2],
                               dtype=tf.float32):
    """Encode groundtruth labels and bounding boxes using SSD anchors from
    one layer.

    Arguments:
      labels: 1D Tensor(int64) containing groundtruth labels;
      bboxes: Nx4 Tensor(float) with bboxes relative coordinates;
      anchors_layer: Numpy array with layer anchors;
      matching_threshold: Threshold for positive match with groundtruth bboxes;
      prior_scaling: Scaling of encoded coordinates.

    Return:
      (target_labels, target_localizations, target_scores): Target Tensors.
    """
    # Anchors coordinates and volume.
    yref, xref, href, wref = anchors_layer
    # yref m x n x 1
    # href num_anchor x 1
    ymin = yref - href / 2. # broadcast offset ymin   m x n x num_anchor
    xmin = xref - wref / 2. # broadcast offset xmin
    ymax = yref + href / 2. # broadcast offset ymax
    xmax = xref + wref / 2. # broadcast offset xmax
    vol_anchors = (xmax - xmin) * (ymax - ymin) # element-wise

    # Initialize tensors...
    shape = (yref.shape[0], yref.shape[1], href.size)
    feat_labels = tf.zeros(shape, dtype=tf.int64)
    feat_scores = tf.zeros(shape, dtype=dtype)

    feat_ymin = tf.zeros(shape, dtype=dtype)
    feat_xmin = tf.zeros(shape, dtype=dtype)
    feat_ymax = tf.ones(shape, dtype=dtype)
    feat_xmax = tf.ones(shape, dtype=dtype)

    # 交的面积/并的面积  m x n x num_anchor
    def jaccard_with_anchors(bbox):
        """Compute jaccard score between a box and the anchors.
        """
        int_ymin = tf.maximum(ymin, bbox[0]) # broadcast 
        int_xmin = tf.maximum(xmin, bbox[1]) # broadcast 
        int_ymax = tf.minimum(ymax, bbox[2]) # broadcast 
        int_xmax = tf.minimum(xmax, bbox[3]) # broadcast 
        h = tf.maximum(int_ymax - int_ymin, 0.)
        w = tf.maximum(int_xmax - int_xmin, 0.)
        # Volumes.
        inter_vol = h * w
        union_vol = vol_anchors - inter_vol \
            + (bbox[2] - bbox[0]) * (bbox[3] - bbox[1]) # broadcast
        jaccard = tf.div(inter_vol, union_vol) 
        return jaccard

    # 交的面积/anchor的面积
    def intersection_with_anchors(bbox):
        """Compute intersection between score a box and the anchors.
        """
        int_ymin = tf.maximum(ymin, bbox[0])
        int_xmin = tf.maximum(xmin, bbox[1])
        int_ymax = tf.minimum(ymax, bbox[2])
        int_xmax = tf.minimum(xmax, bbox[3])
        h = tf.maximum(int_ymax - int_ymin, 0.)
        w = tf.maximum(int_xmax - int_xmin, 0.)
        inter_vol = h * w
        scores = tf.div(inter_vol, vol_anchors)
        return scores

    def condition(i, feat_labels, feat_scores,
                  feat_ymin, feat_xmin, feat_ymax, feat_xmax):
        """Condition: check label index.
        """
        r = tf.less(i, tf.shape(labels))
        return r[0]

    def body(i, feat_labels, feat_scores,
             feat_ymin, feat_xmin, feat_ymax, feat_xmax):
        """Body: update feature labels, scores and bboxes.
        Follow the original SSD paper for that purpose:
          - assign values when jaccard > 0.5;
          - only update if beat the score of other bboxes.
        """
        # Jaccard score.
        label = labels[i]
        bbox = bboxes[i]
        jaccard = jaccard_with_anchors(bbox)
        # Mask: check threshold + scores + no annotations + num_classes.
        mask = tf.greater(jaccard, feat_scores)
        # mask = tf.logical_and(mask, tf.greater(jaccard, matching_threshold))
        mask = tf.logical_and(mask, feat_scores > -0.5)
        mask = tf.logical_and(mask, label < num_classes)
        imask = tf.cast(mask, tf.int64)
        fmask = tf.cast(mask, dtype)
        # Update values using mask.
        feat_labels = imask * label + (1 - imask) * feat_labels
        feat_scores = tf.where(mask, jaccard, feat_scores)

        feat_ymin = fmask * bbox[0] + (1 - fmask) * feat_ymin
        feat_xmin = fmask * bbox[1] + (1 - fmask) * feat_xmin
        feat_ymax = fmask * bbox[2] + (1 - fmask) * feat_ymax
        feat_xmax = fmask * bbox[3] + (1 - fmask) * feat_xmax

        return [i+1, feat_labels, feat_scores,
                feat_ymin, feat_xmin, feat_ymax, feat_xmax]
    # Main loop definition.
    i = 0
    [i, feat_labels, feat_scores,
     feat_ymin, feat_xmin,
     feat_ymax, feat_xmax] = tf.while_loop(condition, body,
                                           [i, feat_labels, feat_scores,
                                            feat_ymin, feat_xmin,
                                            feat_ymax, feat_xmax])
    # Transform to center / size.
    feat_cy = (feat_ymax + feat_ymin) / 2.
    feat_cx = (feat_xmax + feat_xmin) / 2.
    feat_h = feat_ymax - feat_ymin
    feat_w = feat_xmax - feat_xmin
    # Encode features.
    feat_cy = (feat_cy - yref) / href / prior_scaling[0]
    feat_cx = (feat_cx - xref) / wref / prior_scaling[1]
    feat_h = tf.log(feat_h / href) / prior_scaling[2]
    feat_w = tf.log(feat_w / wref) / prior_scaling[3]
    # Use SSD ordering: x / y / w / h instead of ours.
    feat_localizations = tf.stack([feat_cx, feat_cy, feat_w, feat_h], axis=-1)
    return feat_labels, feat_localizations, feat_scores
```

上面是准备真值的过程， 对每一层feature map 的 每一个 feature 准备：
1.  m x n x num_anchor 数组保存 score
2.  m x n x num_anchor 数组保存 bbox_ymin 的真值
3.  m x n x num_anchor 数组保存 bbox_xmin 的真值
4.  m x n x num_anchor 数组保存 bbox_ymax 的真值
5.  m x n x num_anchor 数组保存 bbox_xmax 的真值
6.  m x n x num_anchor 数组保存 feat_labels
7.  feat_localizations 由 2 ~ 6 的数据生成

encode features 是相对于anchor boxes 的 offsets. 这也是前面一直说的，回归的是bbox的offset的原因。



# 使用 tensorflow 进行目标检测

SSD 参考： https://github.com/balancap/SSD-Tensorflow
https://github.com/oyxhust/ssd-text_detection

SSD 是 目标检测 的统一架构。

由三个部分组成：
1. datasets
2. pre-processing:预处理和数据增强，（VGG和Inception的启发）
3. networks

## 2. preprocessing
在 preprocessing 的模块内

## 3. networks
在 nets 模块内
SSD 网络的定义和编码（encoding）和解码(decoding)的过程。

检测过程由两个步骤组成：
1. 在图像上作用SSD模型。
    - 构建模型 ssd_vgg_300
2. 在输出上执行后处理算法。
    - top-k filter
    - Non-maximun Suppression

这里需要使用 transfer learning 中的一些方法。 比如把 VGG的网络迁移到我们的计算过程。

VGG迁移在笔记 `transfer-deep-networks.md` 中。

主要使用的是tf.contrib.slim 中的功能
https://github.com/tensorflow/tensorflow/tree/master/tensorflow/contrib/slim