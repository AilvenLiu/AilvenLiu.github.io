---
layout:     post
title:      Series Articles of Paper Translation  -- 04
subtitle:   Deep Learning for Single Image Super-ResolutionA Brief Review
date:       2020-04-14
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Paper Reading
    - Translation
    - En2Zh
    - Deep Learning
    - Super Resolution
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

> Translated from [Deep Learning for Single Image Super-ResolutionA Brief Review](https://github.com/OUCliuxiang/Books_And_Papers/blob/master/DeepLearning/SuperResulotion/Deep%20Learning%20for%20Single%20Image%20Super-ResolutionA%20Brief%20Review.pdf) -- Wenming Yang, Xuechen Zhang, Yapeng Tian, Wei Wang, Jing-Hao Xue, Qingmin Liao    

## 深度学习于单张图片超分辨率重建：一篇简短的回顾     

### 摘要   
单图像超分辨率(SISR)是一个极具挑战性的病态问题，该问题的目标是通过某种方法从低分辨率(LR)图片中得到高分辨率(HR)的输出。近来，强大的深度学习算法也在SISR领域得以应用并取得了最佳表现SORT。在这篇调查中我们会回顾基于深度学习的SISR方法，并且根据它们在SISR的两个基础方面——用于SISR的高效神经网络结构的探索和对深度SISR学习的高效的优化目标的发展——将它们分为两类。对于每一个类别，我们会先建立一个baseline，再总结该baseline下的重要限制。然后基于各自的原始内容我们会提出克服了这些限制的现象级工作。同时基于不同的发展前景，我们也会对重要的展示和分析、相关的比较等内容做出总结。综述的最后我们会总结利用深度学习方法的SISR的当前挑战和发展趋势。     
* **关键词：**Single image super-resolution, deep learning, neural networks, objective function.    


### 一，引言    

深度学习是机器学习算法的一个分支，目标是学习数据的层次化表现。深度学习已经在诸如计算机视觉、语音识别和自然语言处理等许多人工智能领域展现出相较于其他机器学习算法的显著的优越性。一般来说，深度学习处理大量非结构化数据的强大能力可归功于高效的计算机硬件的发展和复杂算法带来的优势这两个两方面的主要贡献。     

由于一个特定的低分辨率图片可以对应于很多可能的高分辨率图片，而且这些我们希望由低分辨率映射过去的高分辨率空间(大多数情况下指的是自然图片空间)通常是复杂难解的，从而SISR是一个极具挑战性的病态问题。此前面向SISR的工作主要有两方面的不足：一方面是我们追求的从低分辨率到高分辨率空间的映射的定义不清晰，另一方面是建立对于给定的大规模数据的高维映射效率极低。最近，得益于其提取连接低分辨率和高分辨率空间的高效能高级抽象信息的强大能力，基于深度学习的SISR方法在质量和数量上都取得了重大进步。    

在本报告中，我们将尽力对近年来的基于深度学习的SISR算法给出一个概览性的回顾。本文主要注意两个方面；用于SISR的高效的神经网络结构设计，高效的深度SISR学习优化目标。如此分类的原因是，当我们应用深度学习算法去解决一个特定的任务时，最好即考虑通用的深度学习策略，又考虑特定领域的知识。从深度学习的发展前景来看，尽管很多其他的技术比如数据预处理或模型训练技术都非常重要，然而深度学习和SISR领域知识的结合才是成功的关键，并且如此做法也通常和适用于SISR的神经网络结构与优化方法的创新相关联。基于这两个我们着力关注的领域的当前研究基准，我们会从贡献点、实验结果以及我们自己的评价和观点等方面对一些现象级的工作展开讨论。    

文章接下来的部分如此安排：第二章节提出SISR和DL相关的背景概念。第三部分介绍对适用于不同的SISR任务的高效神经网络结构的探索的著作。第四部分研究适用于不同目标的高效的目标函数。第五部分总结基于DL的SISR的发展趋势和挑战。第六部分总结全文。      

##  二，背景     

### 二-A，单图片超分辨率重建     
<img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/paper/SISR-01.png" alt="Figure-1" width="550"/>  
超分辨率指的是从同一场景的一个或多个低分辨率观测中恢复出高分辨率图片的任务。根据输入低分辨率图片的数量不同，超分辨率可以分为单图像超分辨率SISR和多图像超分辨率MISR。相比于MISR，SISR以其高效而更加流行。由于具有高感知质量的高分辨率图片的细节更有价值，其被广泛应用于诸如医疗图片、卫星图片和安保图像等领域。在如图一所描绘的典型SISR框架中，低分辨率图片$y$可建模为:     
$$
y=(x\otimes k)\downarrow_s+n,
\tag{1}
$$     

其中$x\otimes k$是模糊核$k$和未知高分辨率图像$x$的卷积操作，$\downarrow_s$是以$s$为尺度参数的下采样操作符，$n$是独立的噪声项。由于一张低分辨率图片有许多可能的高分辨率图片所对应，从而求解式(1)是一个极其非良置性的问题。截至今天，主流的SISR算法大致可分为三类：基于插值的算法，基于重构的算法，和基于学习的算法。    
基于插值的