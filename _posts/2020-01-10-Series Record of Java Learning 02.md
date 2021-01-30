---
layout:     post
title:      Series Articles of Java Learning  -- 02
subtitle:   Java学习练习02--基于阿里云的普通增值税发票识别工具开发
date:       2021-01-30
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - 学习
    - Java
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

>  写完崇教授的程序后，想趁着还熟悉开发流程，一些如GUI之类的组件也还能复用，趁热再写个发票识别工具。恰好过几天也是李老师生日，当作生日礼物也不错。依然是基于阿里云api开发，但是这次因为定制化程度较高，要从json里面自己找出合适的内容写表格，所以更加麻烦，遇到的问题也更多。项目代码开源在我的[github](https://github.com/OUCliuxiang/Java_related/tree/main/Ticket2Excel)。  
>  这篇博客主要记录项目开发的流程，一些共性的问题和基本语法问题会在其他博客中详细记录。  
