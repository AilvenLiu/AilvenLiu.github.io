---
layout:     post
title:      Series Article of cpp -- 02
subtitle:   next_permutation 需要是有序数组            
date:       2021-09-27
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:     
    - c++       
    - STL
---     

刷 leetcode ，46 题 Permutations 一个作弊的方法是直接使用 C++20 新标准中的 next_permutation API：        
```c++    
do{
    res.push_back(nums);
} while (next_permutation(nums.begin(), nums.end()));       
```        
但需要注意，next_permutation(start, end) API 需要被寻找的数组是有序数组，也即在进入循环之前需要完成一次 sort .