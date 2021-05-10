---
layout:     post
title:      Series Article of Python -- 01
subtitle:   Python实录01 -- 汇总一些小知识         
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

> 记录一些python编程过程中的一些有用的小知识，零零散散，想起来就添一点儿。     


## 判断文件是否为空     

`line = f.readline()`之后判断`line`是否存在，比如现在的任务是剔除一组文件里没有内容的项：     
```python     
for file in fileList:    
    with open(file) as f:    
        line = f.readline()
        if not line:     
            os.remove(filePath)
```   

## 调用系统命令     

```python    
import os 
os.system("system cmd")    
```   

