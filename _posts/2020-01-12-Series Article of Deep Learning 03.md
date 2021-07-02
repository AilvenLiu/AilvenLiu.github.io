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
    def forward(self, x):
        x = x * torch.tanh( F.softplus(x))
        return x
```    

`Mish` 是 yolov5 中 `BottleneckCSP` 和 `BottleneckSSP` 使用的激活函数，注意，常规卷积 `Conv` 模块没有使用 `Mish`。该激活函数出自论文[Mish: A Self Regularized Non-Monotonic Activation Function](https://arxiv.org/pdf/1908.08681.pdf)，由 `softplus` 和双曲正切 `tanh` 组成：$Mish(x) = tanh( softplus(x))$ 。回顾一下双曲正切 tanh 的定义：    

$$    
tanh(x) = \frac{sinh(x)}{cosh(x)} = \frac{e^x - e^{-x}}{e^x + e^{-x}}
$$     
$$    
sinh(x) = \frac{e^x - e^{-x}}{2}, \ \ \ \  cosh(x) = \frac{e^x + e^{-x}}{2}
$$     

tanh关于原点对称，其值限定在 [-1, 1] 范围，且在一定范围内接近线性变换。   

softplus 是一种由指对数函数组成的激活函数： $softplus(x)=log(1+e^x)$ ，其大于零的部分斜率接近 1 ，小于零的部分值向 0 逼近，近似 ReLU 却十分光滑。值得注意的是，在 [`torch` 的实现](https://pytorch.org/docs/stable/generated/torch.nn.Softplus.html)中，额外给了该函数一个参数 $\beta$ ，公式就变成了：$softplus(x)=\frac{1}{\beta}log(1+e^{\beta\cdot x})$。但是，该参数默认是 1。   

论文中给出的 Mish 及其他各相关函数曲线和一二阶导数曲线如下图，     
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/deepL/deepLearning02-Mish.png"></div>      


## Conv    

```python 
class Conv(nn.Module):
    # Standard convolution     
    
    def __init__(self, c1, c2, k=1, s=1, p=None, g=1, act=True):     
    # ch_in, ch_out, kernel, stride, padding, groups      

        super(Conv, self).__init__()
        self.conv = nn.Conv2d(c1, c2, k, s, autopad(k, p), groups=g, bias=False)
        self.bn = nn.BatchNorm2d(c2)
        self.act = nn.Hardswish() if act else nn.Identity()

    def forward(self, x):
        return self.act(self.bn(self.conv(x)))

    def fuseforward(self, x):
        return self.act(self.conv(x))
```      



## Focus    

