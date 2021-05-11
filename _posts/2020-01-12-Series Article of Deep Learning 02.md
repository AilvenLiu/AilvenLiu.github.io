---
layout:     post
title:      Series Article of Deep Learning -- 02
subtitle:   目标检测02 --     
date:       2021-05-08
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

> 参加数据比赛或进行具体目标检测任务的时候，需要自己划分一部分val验证集，用以在训练过程中监控网络精度、敛散性、泛化程度等状态。      

默认待划分的数据集以yolo组织：     

