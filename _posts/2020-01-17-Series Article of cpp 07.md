---
layout:     post
title:      Series Article of cpp -- 07
subtitle:   使用 bitset 容器实现整型数和二进制数之间的转换        
date:       2021-10-07
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:     
    - c++     
    - STL      
---     
> From [StackOverlow](https://stackoverflow.com/questions/571394/how-to-find-out-if-an-item-is-present-in-a-stdvector)
         
You can use std::find from <algorithm>:        
```c++
#include <algorithm>
#include <vector>
vector<int> vec; 
//can have other data types instead of int but must same datatype as item        

std::find(vec.begin(), vec.end(), item) != vec.end()
```       

This returns a bool (true if present, false otherwise). With example:
```c++          
#include <algorithm>
#include <vector>

if ( std::find(vec.begin(), vec.end(), item) != vec.end() )
   do_this();
else
   do_that();
```          
```          
