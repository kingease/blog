---
title: how to use tf.app in notebook
tags:
---

## 标准的tf.app的组件使用


## 触发tf.app的运行
在最后的一个cell中， 模拟命令行，并执行
``` python
FLAGS._parse_flags(['--dataset_dir', '/home/mobile/data/synthtext/',
                    '--split_name', 'train',
                    ])
main(_)
```
它等效于执行脚本中的部分：
``` python
if __name__ == '__main__':
    tf.app.run()
```
然后在命令行执行
```bash
python app.py --dataset_dir=/home/mobile/data/synthtext/ --split_name=train
```
