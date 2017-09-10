---
title: store a model from checkpoint
tags:
---

```
# specify the scope to restore the variables
scope = 'ocr_line_network'
variables = slim.get_variables_to_restore(include=[scope])

saver = tf.train.Saver(variables)
saver.restore(isess, ckpt_filename)
```



