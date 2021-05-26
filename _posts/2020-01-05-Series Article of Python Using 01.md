---
layout:     post
title:      Series Article of Python -- 01
subtitle:   Python实录01 -- 汇总一些小知识         
date:       2021-05-07
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

> 记录一些python编程过程中的一些有用的小知识、小技巧，零零散散，想起来就添一点儿。     


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

## np.ndarray相关     

以任何方法产生或转换`np.array()`数组的时候，建议指定数组数据类型。如建造适用于图像的零数组：    
```python   
imgOut = np.zeros(imgIn.shape, dtype=np.uint8)
```    
转换适用于np操作的浮点数组：     
```python    
imgOut = np.array(imgIn/255., dtype=np.float)
```    
如果需要，也可以建造时不指定，而在使用时另行转换：    
```python    
imgOut = np.uint8(imgOut*255)     
```    

## numpy限定数组的值范围      

图像作为数组执行加噪声操作后，值可能会突破0--255，使用`uint8`类型会报错。此时需要将值强制限定在0--255的范围。     
```python    
imgOut = np.clip(imgOut, 0.0, 1.0)    
# 这里是因为前面将图像数组转换成浮点类型方便添加高斯噪声，     
# clip函数的min, max 参数可以是浮点或整型。     
```   



## numba加速    

`numba`对带循环的方法的处理有奇效，往往可达到几十甚至上百倍的加速。使用也很简单，通过`import numba as nb`导入`nb.jit`修饰器，并通过函数定义上一行添加`@jit`标记使用该修饰器的函数。比如给图片添加椒盐噪声的代码：     
```python    
@nb.jit()
def pepperSalt(imgIn):
    imgOut = np.zeros(imgIn.shape, np.uint8)
    for i in range(imgIn.shape[0]):
        for j in range(imgIn.shape[1]):
            for k in range(imgIn.shape[2]):
                rdn = np.random.random()
                if rdn < 0.025:
                    imgOut[i][j][k] = 0
                elif rdn > 0.975:
                    imgOut[i][j][k] = 255
                else:
                    imgOut[i][j][k] = imgIn[i][j][k]
    return imgOut
```   


有三个需要注意的点：    
1. **@jit只能修饰显示定义的函数，而不能修饰某几条语句或lambda函数。**     
2. **Numba对处理循环有奇效，非循环语句则无法实现较强的加速。**      
3. **Numba只支持了Python原生函数和部分NumPy函数，** 对如opencv等其他库函数则无法加速，会报warning甚至error。     


