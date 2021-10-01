---
layout:     post
title:      Series Article of cpp -- 03
subtitle:   linux下C/C++位操作的轮子：gcc/g++ 中的 __builtin_xx 系列内建函数              
date:       2021-10-01
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:     
    - c++ 
---     

linux 下 使用 gcc/g++ 编译器进行 C/C++ 位操作时常用的几个高效轮子。      

1. `__builtin_ffs( uint32_t x)`： 返回 x 的最后一位 1 是从后向前第几位。        
2. `__builtin_cls( uint32_t x)`： 返回 x 的前导 0 的个数。        
3. `__builtin_ctz( uint32_t x)`： 返回 x 的末尾 0 的个数。          
4. `__builtin_popcount(uint32_t x)`： 返回 x 二进制下 1 的个数。           
5. `__builtin_parity(uint32_t x)`： 返回 x 二进制下 1 的个数的奇偶性。      
