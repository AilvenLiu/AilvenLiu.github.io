---
layout:     post
title:      Series Article of cpp -- 11
subtitle:   C++ 退出多层for循环        
date:       2021-10-10
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:     
    - c++     
---     
刷 leetcode 212 题 Word Search II，起初想法用一个双层循环去做（当然结果 TLE 了），但最里面一层循环满足条件后怎么退出到最外层循环给我难住了：          

将多层 for 循环写到一个子函数里面，然后用return 进行返回，退出子函数，完成跳出多层循环。           