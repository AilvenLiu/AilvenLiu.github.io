---
layout:     post
title:      Series Article of Python Using -- 08
subtitle:   Python实录08 -- 'str' object does not support item assignment        
date:       2021-11-11
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - python   
---

```python      
s1 = "Hello World"
s2 = ""
j = 0

for i in range(len(s1)):
    s2[j] = s1[i]
    j = j + 1
```
报错:           
TypeError: 'str' object does not support item assignment        
 
曲线救国方案：         

```python 
>>> str1 = "mystring"
>>> list1 = list(str1)
>>> list1[5] = 'u'
>>> str1 = ''.join(list1)
>>> print(str1)
mystrung
>>> type(str1)
<type 'str'>
```