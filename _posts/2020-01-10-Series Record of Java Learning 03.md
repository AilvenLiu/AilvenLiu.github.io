---
layout:     post
title:      Series Articles of Java Learning  -- 03
subtitle:   Java学习实录03--一些基本知识
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

>  本文旨在对使用**IDEA**进行java项目开发过程中遇到的一些共性问题进行总结。  

## jar包的引入    
当项目需要非默认的功能时，需要引入第三方jar包。  
1. 在[maven](https://mvnrepository.com/)查询并下载指定版本号的File->jar包，如下图所示：   
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/javaSeries/java-001.png"></div>   

2. 将下载好的jar包放入项目的`lib`目录，也即与`scr/`, `out/`平级的路径，如下图所示。实际上，加入包到项目下任何路径均可，且如果直接加入到根路径，似乎无需下一步导入操作。   
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/javaSeries/java-002.png"></div>    

3. 导入包到项目。File --> Project Structure --> 左侧栏Modules --> 右侧栏Dependencies --> 最右侧栏“+”号 --> 1.JARs or directories... --> 选中lib中的包 --> OK --> Apply --> OK. 完成导入。过程中部分步骤如下图所示：     
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/javaSeries/java-003.png"></div> <div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/javaSeries/java-004.png"></div>    

   