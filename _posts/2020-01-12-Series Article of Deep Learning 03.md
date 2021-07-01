---
layout:     post
title:      Series Article of Deep Learning -- 03
subtitle:   目标检测03 -- yolov5诸模块简易解析     
date:       2021-05-07
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - pytorch
    - object detection
---

<head>
    <script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
    <script type="text/x-mathjax-config">
        MathJax.Hub.Config({
            tex2jax: {
            skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
            inlineMath: [['$','$']]
            }
        });
    </script>
</head>   

> yolov5还是有一些有意思的东西的，做一个简单的解析，给自己看。      

## yolov5网络结构图    

<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/deepL/deepLearning01.png"></div>     

其中 `BackBone` 阶段主要包括 `Focus, Conv, BottleneckCSP, SPP` 等几个模块， `Head` 阶段主要包括 `BottleneckCSP, Conv, nn.Upsample, Concat, nn.Conv2d, Detect` 等几个模块。其中 `nn.xxx` 来自 `torch.nn`， 是 pytorch 的原生模块，其他的则在 `models/commons.py` 文件中定义。      
 
`models/commons.py` 中还定义了许多其他模块，可能是别的地方用的吧？一并进行解析。下面一个个儿的来。    


## Mish     

```python     
class Mish(nn.Module):
    def __init__(self):
        super().__init__()
    def for
```    



`Mish` 是yolov5 使用的激活函数，出自论文[Mish: A Self Regularized Non-Monotonic Activation Function](https://arxiv.org/pdf/1908.08681.pdf)。论文中给出的函数曲线和一二阶导数曲线如下图，     
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/deepL/deepLearning02-Mish.png"></div>      

可以看出 Mish 的曲线和

## Conv    


## Focus    

