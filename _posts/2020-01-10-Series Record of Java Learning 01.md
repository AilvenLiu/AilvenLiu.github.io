---
layout:     post
title:      Series Articles of Java Learning  -- 01
subtitle:   Java学习练习01--基于阿里云的表格识别OCR工具开发
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

>  受崇教授所托，开发一款图片转表格OCR工具。考虑到时正在学习java，从头做个小项目并发布成exe正好能当成个练习。同时，由于崇教授只给了一天半的时间，难以从头训练一个神经网络，更遑论表格线分割等外围任务。综合考虑，决定调用阿里云的api，使用其java接口进行二次开发。项目代码开源在我的[github](https://github.com/OUCliuxiang/Java_related/tree/main/AliTableOCR)。  
>  这篇博客主要记录项目开发的流程，一些共性的问题和基本语法问题会在其他博客中详细记录。  


## API选择   
综合比较了华为、腾讯和阿里云的效果及封装方便程度（也即使用时的方便程度），选择了阿里云市场的[一款产品](https://market.aliyun.com/products/57124001/cmapi024968.html?spm=5176.2020520132.101.8.6af772181XygKX#sku=yuncode1896800000)。事实证明，这款产品很好用，对低质量图片的识别效率高效果好。  
这个API最方便的地方在于，处理好的excel表格可以通过base64编码偶不是json数据类型返回（虽然也需要从json读出base64码），这样以来，我们可以直接通过`org.apache.commons.codec`包中的`Base64.encodeBase64`将从json解码出表格文件。这里涉及到从零开始java项目过程遇到的第一个问题，[如何引入和使用初始环境不包含的jar包]()。  


## 实际开发过程   
整个项目的结构大致如下：   
AliTableOCR   
|  
|--data   
|--lib     
|--out    
|--src    
|  |--com    
|  |  |--alibaba.ocr.damo
|  |  |  |--AliTableOCR  
|  |  |  |--Base64excel  
|  |  |   
|  |  |--aliyun.api.gateway.demo.util  
|  |  |  |--HttpUtils  
|  |  |  |--pom.xml  
|  |  |  
|  |  |--gui   
|  |  |  |--FileChooser   
|  
|--jars...
