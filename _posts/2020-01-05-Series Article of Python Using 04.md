---
layout:     post
title:      Series Article of Python Using -- 04
subtitle:   Python实录04 -- 删除文件、清空目录的方法总结         
date:       2021-09-10
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - python   
---

> 转载自 CSDN 博客 [python 删除文件、清空目录的方法总结](https://blog.csdn.net/JohinieLi/article/details/80450164)         

## os.remove() 方法       
os.remove() 方法用于删除指定路径的文件。如果指定的路径是一个目录，将抛出OSError。     
在Unix, Windows中都有效。以下实例演示了 remove() 方法的使用：      
```python      
#!/usr/bin/python     

# -*- coding: UTF-8 -*-     
 
import os, sys

# 列出目录      

print ("目录为: %s" %os.listdir(os.getcwd()))

# 移除      

os.remove("aa.txt")

# 移除后列出目录      

print ("移除后: %s" %os.listdir(os.getcwd()))
```        

执行以上程序输出结果为：      
```bash     
目录为:     
[ 'a1.txt','aa.txt','resume.doc' ]     
移除后:      
[ 'a1.txt','resume.doc' ]     
```      

## os.removedirs() 方法       
os.removedirs() 方法用于递归删除目录。像rmdir(), 如果子文件夹成功删除, removedirs()才尝试它们的父文件夹,直到抛出一个error(它基本上被忽略,因为它一般意味着你文件夹不为空)。       
以下实例演示了 removedirs() 方法的使用：       
```python    
#!/usr/bin/python      
# -*- coding: UTF-8 -*-      

import os, sys

# 列出目录     

print "目录为: %s" %os.listdir(os.getcwd())

# 移除      
os.removedirs("/test")

# 列出移除后的目录      

print "移除后目录为:" %os.listdir(os.getcwd())
```     

执行以上程序输出结果为：      
```bash     
目录为:
[  'a1.txt','resume.doc','a3.py','test' ]
移除后目录为:
[  'a1.txt','resume.doc','a3.py' ]
```      

## os.rmdir() 方法     
os.rmdir() 方法用于删除指定路径的目录。仅当这文件夹是空的才可以, 否则, 抛出OSError。     
以下实例演示了 rmdir() 方法的使用：     
```python     
#!/usr/bin/python        

# -*- coding: UTF-8 -*-         

import os, sys

# 列出目录     

print "目录为: %s"%os.listdir(os.getcwd())

# 删除路径      

os.rmdir("mydir")

# 列出重命名后的目录       

print "目录为: %s" %os.listdir(os.getcwd())
```      

执行以上程序输出结果为：     
```bash     
目录为:
[  'a1.txt','resume.doc','a3.py','mydir' ]
目录为: 
[  'a1.txt','resume.doc','a3.py' ]    
```     

## os.unlink() 方法       
os.unlink() 方法用于删除文件,如果文件是一个目录则返回一个错误。这个方法和 remove() 方法效果几乎完全一致，目前尚没有发现效果不一致的情况。         
以下实例演示了 unlink() 方法的使用：       
```python     
#!/usr/bin/python       

# -*- coding: UTF-8 -*-       

import os, sys

# 列出目录     

print "目录为: %s" %os.listdir(os.getcwd())

os.unlink("aa.txt")

# 删除后的目录       

print "删除后的目录为: %s" %os.listdir(os.getcwd())
```      

执行以上程序输出结果为：      
```bash     
目录为:       
[ 'a1.txt','aa.txt','resume.doc']      
删除后的目录为: 
[ 'a1.txt','resume.doc' ]
```      

## os.remove() / os.rmdir() 递归清空文件夹      
调用系统方法执行递归逻辑，给出实例如下：      
```python       
import os
for root, dirs, files in os.walk(topPath, topdown=False):
    for name in files:
        os.remove(os.path.join(root, name))
    for name in dirs:
        os.rmdir(os.path.join(root, name))
```     

## shutil 清空指定文件夹下所有文件      
这个需求很简单：需要在执行某些代码前清空指定的文件夹，如果直接用os.remove()，可能出现因文件夹中文件被占用而无法删除，解决方法也很简单，先强制删除文件夹，再重新建同名文件夹即可：       
```python     
import shutil   
shutil.rmtree('要清空的文件夹名')     
os.mkdir('要清空的文件夹名')    
```     

值得一提的是，shutil 库非常实用，例如其中的 copy, remove 等方法允许以非常人类友好的调用进行复制、移动和重命名等操作。但鉴于其过于简单，不在另费篇幅介绍。      