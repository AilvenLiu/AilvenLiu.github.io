---
layout:     post
title:      Linux C/C++ 学习笔记 -- 02          
subtitle:   getcwd 获取当前路径               
date:       2022-04-06
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Linux C/C++ Programming     
    - WebServer
--- 

Linux C/C++ 编程中可以通过 `getcwd()` 获取当前绝对路径。函数定义为：           
```c++
char *getcwd(char *buf, size_t size);
```        

**参数**：       
* `*buf`：保存当前目录的缓冲区，通常是个 char 数组           
* `size`：buf 数组的长度。       

**返回值**：成功则返回指向当前目录的指针，和 buf 的值一样，错误返回NULL。但不必接收。     

**实例**：        
```c++
char server_path[200];
getcwd(server_path, 200);
```