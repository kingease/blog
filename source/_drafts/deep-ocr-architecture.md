---
title: deep ocr architecture
tags:
---


# 工作流的主要组成部分

1. doc detection
2. rectification
3. word detector
4. word recognization
5. attention filter


##  3. 词的侦测 Word Detector

### 传统的计算机视觉方法 （对字体宽度不大的字，很容易丢失）
MSER(最大稳定极值区域)，该方法在不同的阈值下(thresholds or levels), 来寻找斑块(blobs), 对文本而言，是一个很好的方法。

简易环境的方法和样例：
文章：http://www.danvk.org/2015/01/07/finding-blocks-of-text-in-an-image-using-python-opencv-and-numpy.html
对应的代码：
https://github.com/danvk/oldnyc/blob/master/ocr/tess/crop_morphology.py

scikit-image 图像处理
http://feram.co/scikit-image-cheatsheet/

document scanner
http://www.pyimagesearch.com/2014/09/01/build-kick-ass-mobile-document-scanner-just-5-minutes/

#### 一个实现的code
https://github.com/AwesomeLemon/document-recognition
以及对应的文章
https://awesomelemon.github.io/2017/01/15/Document-recognition-with-Python-OpenCV-and-Tesseract.html

试用了代码，发现可以抓取感兴趣的区域，但是不太稳定。
`cv2.connectedComponentsWithStats` 可以在binary上找连续区域，
但要依赖于好的的binary,
压线的情况有时不利于连续区域的搜索。


### 基于SSD深度网络的方法
目前计划使用 SSD （single short dectection）的方法做文字部分的检测。
#### SSD 
参考： https://github.com/balancap/SSD-Tensorflow
详细在笔记 Single-Shot-MultiBox-Detector-in-TensorFlow.md中讨论

#### word detection
参考：https://github.com/oyxhust/ssd-text_detection

### attention_ocr
参考：https://github.com/tensorflow/models/tree/master/attention_ocr
tensorflow google 的 一种方法。

### 另外一个 Attention-OCR
这个应该属于 WDN 部分的一个实现。
https://github.com/da03/Attention-OCR 



## 4. 词的深度网 Word Deep Net

### 深度网
1. CNN
2. LSTM
3. CTC
4. batch normalization 


input images:
1. 长和宽的长度是固定的（以后更长的需要，主动切断图像，目前不在考虑范围之内）
2. 目前只支持灰白图（减少输入的变化范围）


### 生成训练数据
1. 生成足够真实的图像是很困难的。目前一个可能的方法是GANS
2. 在我们的问题里，使用合成的图像是适用的。
3. 文字和它的变化是有限的。

合成数据的流程：
1. 所用词的语料 这里主要是数字，小数点，符号
2. 所使用的字体
3. 一组几何(gemoetric)和光度(photometric)的变化来模拟真实场景带来的变化
4. a large number of visual transformations, such as warping, fake shadows, and fake creases, and much more.
5. 如何生成数据集，参考：http://www.robots.ox.ac.uk/~vgg/data/text/#sec-chars
6. 上述数据生成代码可能是：https://bitbucket.org/jaderberg/text-renderer

