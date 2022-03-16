---
layout:     post
title:      Series Article of Algorithm and Data Structure -- 02 
subtitle:   快排和归并     
date:       2022-03-16
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Algorithm      
--- 

> from ACWing [785. 快速排序](https://www.acwing.com/problem/content/819/)， [787. 归并排序](https://www.acwing.com/problem/content/821/)， [788. 逆序对的数量](https://www.acwing.com/problem/content/790/)， [803. 区间合并](https://www.acwing.com/problem/content/description/805/)。          

### 快排基础        
快排基于分治策略。         
给定数组 `arr[]` 和区间 `[l, r]`，在该区间内随机找一个锚定点 `x = arr[idx]`。需要保证某一个下标点 i ，此点之前元素值均小于等于 x， 此点之后均大于等于 x 。这样就将数组划分成了两个区间：[l, i], [i, r]，其中前一个区间全部元素小于等于后一个区间全部元素，然后递归调用，对被划分的两个子区间排序，直到数组整体有序。           

#### 具体实现        

使用随机数随机选取 [l, r] 中的坐标点： `idx = rand() % (r-l+1) + l;`            
初始化上下界指针