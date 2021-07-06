---
layout:     post
title:      Series Article of Deep Learning -- 03
subtitle:   目标检测03 -- yolov5 中的 tensor 切片操作报错问题     
date:       2021-07-05
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

> u版yolov5有一个检测框定位转换函数，某些特殊情况下会抛出 senmentation fault 错误。





