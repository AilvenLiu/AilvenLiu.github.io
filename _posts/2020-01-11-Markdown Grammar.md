---
layout:     post
title:      Markdown Grammar
subtitle:   一些Markdown语法记录
date:       2021-04-37
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - 博客
    - github
    - Markdown
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

> 使用markdown书写github博客过程中遇到的问题记录，主要是记录一点儿不熟悉的语法。随时补充。     


**`> `**： 右向尖括号后面的内容是引用，适合用在文章开头。     

**表格**： Markdown 制作表格使用“ | ”来分隔不同的单元格，使用“ - ”来分隔表头和其他行。语法格式如下：    
 \|  表头  \|  表头  \|    
 \|  ----  \| ----  \|    
 \|  单元格 \| 单元格 \|    
 \|  单元格 \| 单元格 \|      

效果如下：    

 |  表头  |  表头  |    
 |  ----  | ----  |    
 |  单元格 | 单元格 |    
 |  单元格 | 单元格 |    

By the way，如同“语法格式如下”到“效果如下”之间的内容，也即，当我们想要以原始文本而不是渲染表现展示markdown的语法符号，使用反斜杠 ‘\’ 进行反义。     

**表格内容对齐**：    
通过表头与内容之间的冒号与短横杠组合设置表格的对齐方式：   

冒号在右边： 设置内容和标题栏居右对齐。    
冒号在左边： 设置内容和标题栏居左对齐。    
冒号在两边： 设置内容和标题栏居中对齐。   

\| 左对齐 \| 右对齐 \| 居中对齐 \|    
\| :-----\| ----: \| :----: \|    
\| 单元格 \| 单元格 \| 单元格 \|    
\| 单元格 \| 单元格 \| 单元格 \|    

**表格内换行**    
当内容过长，需要主动换行。换行符`<br>`。


**github博客中渲染数学公式**    

github博客默认markdown页面不支持渲染数学公式，需要再每篇文章开头添加如下内容：    
```javascript
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
```     

