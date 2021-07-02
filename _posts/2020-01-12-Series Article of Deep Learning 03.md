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

## 概述         

先放一张网络图镇宅。     
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/deepL/deepLearning01.png"></div>     

图中 `BackBone` 阶段主要包括 `Focus, Conv, BottleneckCSP, SPP` 等几个模块， `Head` 阶段主要包括 `BottleneckCSP, Conv, nn.Upsample, Concat, nn.Conv2d, Detect` 等几个模块。其中 `nn.xxx` 来自 `torch.nn`， 是 pytorch 的原生模块，其他的则在 `models/commons.py` 文件中定义。      
 
`models/commons.py` 中还定义了许多其他模块，可能是别的地方用的吧？一并进行解析。下面按照 `models/commons.py` 中的顺序一个个儿来。   

### 目录     

给个目录，万一有人看呢。        
1. Mish      
2. Conv     
3. BottleneckCSP    
4. BottleneckCSP2    
5. VoVSP       
6. SPP      
7. SPPCSP       
8. MP    
9. Focus       
10. Concat     
11. NMS      
12. autoShape      
13. Flatten       
14. Classify      
15. Detect      




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
$tanh(x) = \frac{sinh(x)}{cosh(x)} = \frac{e^x - e^{-x}}{e^x + e^{-x}},$
$\ \ \ sinh(x) = \frac{e^x - e^{-x}}{2}, \ \ \ \  cosh(x) = \frac{e^x + e^{-x}}{2}$     
github page 中双美元符号 `$$` 包裹的公式块无法渲染，只能用单美元符号 `$` 包裹的行内公式凑活一下，不好看。    
$$
\frac{test}{Equation}
$$

tanh关于原点对称，其值限定在 [-1, 1] 范围，且在一定范围内接近线性变换。   

softplus 是一种由指对数函数组成的激活函数： $softplus(x)=log(1+e^x)$ ，其大于零的部分斜率接近 1 ，小于零的部分值向 0 逼近，近似 ReLU 却十分光滑。值得注意的是，在 [`torch` 的实现](https://pytorch.org/docs/stable/generated/torch.nn.Softplus.html)中，额外给了该函数一个参数 $\beta$ ，公式就变成了：$softplus(x)=\frac{1}{\beta}log(1+e^{\beta\cdot x})$。但是，该参数默认是 1。   

论文中给出的 Mish 及其他各相关函数曲线和一二阶导数曲线如下图，     
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/deepL/deepLearning02-Mish.png"></div>      


## Conv    

```python    
# Standard convolution      

class Conv(nn.Module):     
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

代码来看，yolov5 中定义的 `Conv` 类就是 `nn.Conv2d( [argvs ...])` 类的简单封装。默认使用 `bn` 层，当然，注意 `bn` 层的参数是 `c2 = channel_output` 。通过 `bool` 类型参数 `act` 确定是否使用激活层，当期值为 `act = False` ，使用 `nn.Indentity()` 做激活函数，也即不激活；如果其值为默认值 `act = True` ，则激活函数为 `nn.Hardswich()`：    
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/deepL/deepLearning03-Hardswish.png"></div>        

破东西。根据[pytorch Documents](https://pytorch.org/docs/stable/generated/torch.nn.Hardswish.html)，该激活函数出自 [MobileNetV3](https://arxiv.org/pdf/1905.02244.pdf)，臭名昭著的负优化网络。怪不得使用原生yolov5跑检测的时候结果总是不尽人意。这里，使用的时候要改，改成经过时间检验的激活函数。    

`Conv` 默认的卷积核是 1 ，也即使用 $1\times 1$卷积。指定了分组卷积参数 $g$ ，但其默认参数为 $g=1$ ，反正就是不分组；当然，可以自己另行指定 $g$ 参数改为使用分组卷积。由于 `pytorch` 原生的 `nn.Conv2d()` 卷积无法通过 `padding=same` 这一类的参数指定 padding 值（可见 pytorch [官方文档](https://pytorch.org/docs/stable/generated/torch.nn.Conv2d.html)），`Conv` 使用了自定义函数 `autopad(k, p=None)` 指定 `padding` 值。    

`common.py` 中还基于 `Conv` 实现了 `DepthWise Convolution` ，但不知道在哪儿使用的。    
### autopad    
```python    
def autopad(k, p=None):  # kernel, padding     

    # Pad to 'same'     

    if p is None:
        p = k // 2 if isinstance(k, int) else [x // 2 for x in k]  
    return p
```    
用来实现 `nn.Conv2d` 中 `padding=same` 的函数。


## Focus    

