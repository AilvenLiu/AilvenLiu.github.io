---
layout:     post
title:      Linux C/C++ 学习笔记 -- 03          
subtitle:   strcpy 函数和 strcat 函数               
date:       2022-04-06
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Linux C/C++ Programming     
    - WebServer
    - c++
--- 

### strcat      

`strcat()`函数包含于 `stdio.h` 和 `string.h` 头文件。函数原型是 ：       
```
extern char *strcat(char *dest, const char *src);
```
功能是把 src 所指字符串添加到 dest 结尾处（覆盖 dest 结尾处的'\0'）。返回指向新字符串头的指针，可以不接收：              

```c++ 
char dest[20]="GoldenGlobal"; 
char *src="View"; 
strcat(dest, src);
printf（"%s",dest）；
``` 
得到输出 dest 为 `GoldenGlobalView` ，中间无空格。         

**注意：**           
dest 和 src 所指内存区域不可以重叠且 dest 必须有足够的空间来容纳 src 的字符串。         

### strcpy            

`strcpy()`函数包含于 `stdio.h` 和 `string.h` 头文件。函数原型是：        
```c++
char *strcpy(char* dest, const char *src);
```
用于把从 src 地址开始且含有 NULL 结束符的字符串复制到以 dest 开始的地址空间。       
**注意：**          
src 和 dest 所指内存区域不可以重叠且 dest 必须有足够的空间来容纳 src 的字符串。
返回指向 dest 的指针。       