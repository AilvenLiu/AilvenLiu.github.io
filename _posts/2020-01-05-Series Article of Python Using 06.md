---
layout:     post
title:      Series Article of Python Using -- 06
subtitle:   Python实录06 -- 字典排序         
date:       2021-09-12
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - python   
---

> 转载自 cnblogs [python修改文件内容3种方法](https://www.cnblogs.com/wc-chan/p/8085452.html)         

## 修改原文件              
```python     
def alter(file,old_str,new_str):
    """     
    
    替换文件中的字符串       

    :param file:文件名      
    
    :param old_str:就字符串      
    
    :param new_str:新字符串      
    
    :return:      
    
    """      

    file_data = ""
    with open(file, "r", encoding="utf-8") as f:
        for line in f:
            if old_str in line:
                line = line.replace(old_str,new_str)
            file_data += line
    with open(file,"w",encoding="utf-8") as f:
        f.write(file_data)

alter("file1", "09876", "python")
```      

## 写新文件       

### 字符串替换     
```python     
import os
def alter(file,old_str,new_str):
    """
    
    将替换的字符串写到一个新的文件中，然后将原文件删除，新文件改为原来文件的名字
    
    :param file: 文件路径     
    
    :param old_str: 需要替换的字符串     
    
    :param new_str: 替换的字符串     
    
    :return: None     
    
    """     

    with open(file, "r", encoding="utf-8") as f1,open("%s.bak" % file, "w", encoding="utf-8") as f2:
        for line in f1:
            if old_str in line:
                line = line.replace(old_str, new_str)
            f2.write(line)
    os.remove(file)
    os.rename("%s.bak" % file, file)

alter("file1", "python", "测试")      
```        

### 正则表达替换       
```python     
import re,os
def alter(file,old_str,new_str):
    with open(file, "r", encoding="utf-8") as f1, 
         open("%s.bak" % file, "w", encoding="utf-8") as f2:
        for line in f1:
            f2.write(re.sub(old_str,new_str,line))
    os.remove(file)
    os.rename("%s.bak" % file, file)
alter("file1", "admin", "password")
```     