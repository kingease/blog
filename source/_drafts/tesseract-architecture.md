---
title: ocr and tesseract
tags:
---

# tesseract
## 处理过程的pipeline

1. 连续组件(connected component)分析，组件的轮廓被保存下来, 称为Blobs，可以嵌套。
2. Blob 由 文字行(text lines) 行(lines) 和 区域（regions）组成。
3. text lines 根据空格被分解成 词(words)。
4. 固定字宽按字符分割；非等宽按模糊空格(fuzzy space)分割。
5. 识别是两个pass的过程。
   - 每个字由 adaptive classifier 进行识别

参考的 ocr engine 的 PPT

识别程序入口文件
tesseract/api/tesseractmain.cpp

训练数据生成的入口
tesseract/training/tesstrain.sh

lstm训练程序入口
tesseract/training/lstmtraining.cpp


## 如何生成训练数据
tesseract/training/text2image.cpp

### 编译生成工具
1. 安装pango `brew install pango`
2. 如果找不到pango 但在/usr/local/lib 已经有pango的文件了, 则执行 `LIBRARY_PATH=$LIBRARY_PATH:/usr/local/lib make train`
3. make training
4. make training install

## 目前了解的训练模式
1. 把图片准备好
2. 用tesseract 命令生成识别的结果以及对应的box文件
3. 用 jTessBoxEditor 工具修改识别结构和box位置
4. 需要执行一系列命令生成 
    5. （http://www.joyofdata.de/blog/a-guide-on-ocr-with-tesseract-3-03/）
    6. （https://blog.callmewhy.com/2015/11/16/use-tesseract-for-ocr/）

## line的寻找

# ocr 资料
