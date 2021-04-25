---
layout:     post
title:      Series Articles of Java Learning  -- 02
subtitle:   Java项目实录02 -- 基于阿里云的普通增值税发票识别工具开发
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

>  写完崇教授的程序后，想趁着还熟悉开发流程，一些如GUI之类的组件也还能复用，写个发票识别工具。
>  这篇博客主要记录项目开发的流程，一些共性的问题和基本语法问题会在其他博客中详细记录。  

# 项目结构    
```    
Ticket2Excel    
|   
|--GenereteExcel        
|  |    
|  |--Generate    
|  |--Ticket2Excel     
|   
|--utils    
|  |     
|  |--Base64Utils     
|  |--HttpUtils     
|  |--PicUtils     
|  |--Utils    
|    
|--gui    
|  |--FileChooser    
```    

# 代码解析    
已经在[上一篇项目实录](https://www.ouc-liux.cn/2021/03/31/Series-Record-of-Java-Learning-01/)中介绍过的内容不再赘述，但会给出提示。    

## 主函数类： Ticket2Excel     

### main函数: main()    

`new Runnable(){...}`线程安全启动不再赘述。    

### 静态方法 submit2AliAPI(String, String)     

定义适用于http传输的`NameValuePair`（来自包`org.apache.http`）数组链表：   
```java   
List<NameValuePair> params = new ArrayList<NameValuePair>();   
```   
关于`List`和`ArrayList`之间关系的小知识，见博客[java常见方法总结](https://www.ouc-liux.cn/2021/01/31/Series-Record-of-Java-Learning-04/)