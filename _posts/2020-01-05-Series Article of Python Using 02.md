---
layout:     post
title:      Series Article of Python -- 01
subtitle:   Python实录01 -- 汇总python-opencv知识        
date:       2021-05-25
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - python   
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

> 记录一些python-opencv使用过程中一些有用的小知识、小技巧，零零散散，想起来就添一点儿。     

## cv.imread()读取图像的像素值     
cv.imread()返回值类型为`uint8`的数组，也就是其读取图像的像素值为0--255之间的整型。

## cv.waitKey(delay)的使用和作用    

使用`cv.imshow('windowName', img)`显示图片的时候，如果不加`cv.waitKey()`，则会有窗口一闪即逝，并不停留。于是需要使用`cv.waitKey()`保留窗口显示在屏幕上。    

### case 1：      

```python    
cv.imshow('windowName', img)
cv.waitKay()   
```   
图片一直显示，直到按下任意一个键退出显示。     

### case 2：    

```python    
cv.imshow('windowName', img)    
cv.waitKey(1000)
```    
图片显示一秒后自动退出，如果期间按下任意一个键，也会退出显示。    

### case 3：    

```python     
sucess, frame = cap.read()
while sucess:    
    cv.imshow('windowName', frame)     
    sucess, frame = cap.read()     
```    
直观上的表现是不显示，实际上是显示了，但没有完全显示，一闪即过。    

### case 4：     

```python    
sucess, frame = cap.read()    
while True:    
    cv.imshow('windowName', frame)   
    cv.waitKey(n)   # n's integer type,  and expected to be small.   
    sucess, frame = cap.read()    
```   
显示，但一直显示，只能在终端ctrl+A强制中断。     

### case 5：     

```python     
sucess, frame = cap.read()     
while True:     
    cv.imshow('windowName', frame)     
    if cv.waitKey(1) & 0xFF == ord('q'):    
        break;
    sucess, frame = cap.read()    
```     
显示，指导终端检测到`q`键被按下，退出循环。    