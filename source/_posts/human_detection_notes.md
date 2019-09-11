---
title: 人的检测与识别
date: 2019-09-11 12:00:00
tags: deep learning
---

# 人的检测与识别
两种途径 检测人脸 + 人体骨架的检测与识别
## 人体的检测与姿态的识别
* https://github.com/wangzheallen/awesome-human-pose-estimation

## 人脸的检测与识别
Face Recognition(人脸识别) 相关的操作有：
10. Face Detection
11. Face Landmarkding
20. Face Alignment
30. Face Recognition
40. Face Attribution
    40.1 expression 表情
    40.2 age 年龄
综述：
[https://arxiv.org/pdf/1804.06655.pdf] Deep Face Recognition: A Survey (2019) 

根据 RetinaFace 的定义
Face localisation 有更广泛的含义，包含如下阶段：
1. Face detection
2. face alignment
3. pixel-wise face parseing
4. 3D dense correspondence regression

### https://github.com/Alireza-Akhavan/deep-face-recognition
基础资料 Deep Face Recognition using Tensorflow workshop material

### 人脸识别 state of the art 工作
- 人脸检测(Face detection):

    [https://arxiv.org/abs/1905.00641] RetinaFace: Single-stage Dense Face Localisation in the Wild

- 人脸对齐(Face alignment)
    [https://arxiv.org/abs/1812.01936] Stacked Dense U-Nets with Dual Transformers for Robust Face Alignment

- 人脸识别(Face recognition)

    [https://arxiv.org/abs/1801.07698] ArcFace: Additive Angular Margin Loss for Deep Face Recognition

参考
* https://github.com/deepinsight/insightface
* https://github.com/deepinsight/insightface/tree/master/RetinaFace

#### [RetinaFace: Single-stage Dense Face Localisation in the Wild]
问题特点
1. 通用的物体检测（object detection）适用于该问题
2. 检测盒的anchor box 的比例 变化比较小一般是 1:1 or 1:1.5
3. 尺度变化比较大

one stage vs two stages
1. faster RCNN two stage
2. SSD one stage : 在训练的时候，正负样本极不平衡 => 采样+变权重

single stage improvement 使用以下三个特点
1. multi-task losses
2. strong superivsed
3. self-superivised

处理的组成部分
1. face classification
2. face box prediction/regression
3. facial landmark regression (supervised)
4. Dense Face regression (self-supervised)

deformable convolution network(DCN) 
使用deformable layer 处理几何变换

刚体(rigid)和非刚体(deformable)的context建模是互补的。

#### [MTCNN Multi-task Cascaded(串联) Convolution Network] 
以前的方法 忽略了 face detection 和 face alignment 两个任务之间的相关性。
multi-task learning consists of three stages:
1. 用一个浅的CNN提取可能的区域(candidate windows) => fast Propossal Network (P-Net)
    1.1 fully convolutional network

2. 从上述的windows中快速排出不包含人脸的区域，用一个稍复杂的CNN => Refinment Network (R-Net)
3. 最后用一个更强大的CNN来预测结果和5个脸部特征点。=> Output Network (O-Net)

Online hard sample mining

reference:
https://kpzhang93.github.io/MTCNN_face_detection_alignment/paper/spl.pdf

### 移动端的or轻模型
* https://github.com/becauseofAI/MobileFace


# 人的性别
# 人的年龄
https://github.com/Alireza-Akhavan/deep-face-recognition#age-estimation-dataset
