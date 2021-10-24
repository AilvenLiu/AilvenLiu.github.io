---
layout:     post
title:      Series Article of cpp -- 15
subtitle:   find(pos1, pos2, elem)查找不到恒返回 end          
date:       2021-10-24
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:     
    - c++   
    - STL
---     

`std::find(pos1, pos2, elem)` 函数用于在前闭后开的 `[pos1, pos2)` 的区间内查找元素 elem 。如果查找成功，返回查找成功元素的指针；如果失败，则恒返回容器的尾端 end() 指针，而和 pos2 无关。如：         
```c++           
// vector<int> nums = [1,2,3,4,5,6,7,8,9,9,9]
std::find(nums.begin(), nums.begin()+5, *(nums.begin()+5));         
```         
返回的值是 `nums.end()` 而非 `nums.begin()+5`。         