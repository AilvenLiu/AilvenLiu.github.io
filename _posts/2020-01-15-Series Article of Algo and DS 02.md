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

#### 实现方案        

使用随机数随机选取 [l, r] 中的坐标点： `idx = rand() % (r-l+1) + l;`。            
初始化上下界下标为 `i = l-1, j = r+1;` 以方便 `do-while` 处理。             
`i, j` 分别向后向前逼近，直到发生交叉。期间如果存在 `arr[i] < x, arr[j] > x`，交换 `arr[i]` 和 `arr[j]` 。          
下标逼近过程停止的位置，要么 `i = j`, 要么 `i = j+1`。以 `j, j+1` 作为下一次细分区间的上下界，可以避免边界问题如下：       
对形如`[a, a]` 的子序列，下标逼近的结果必然是 `j=0, i=1;`，若以`i` 为下次细分数组的上界，则左子序列陷入循环处理 `[a, a]` 的死循环。          


#### 模板           

```c++
void quick_sort(int arr[], int l, int r){
    if (l >= r) return; // 区间没有或只有一个元素         
    int idx = rand() % (r - l + 1) + l;

    int x = arr[idx], i = l - 1, j = r + 1;

    while( i < j){
        do i++; while ( arr[i] < x);
        do j--; while ( arr[j] > x);
        if (i < j)  std::swap(arr[i], arr[j]);
    }

    quick_sort(arr, l, j);
    quick_sort(arr, j+1, r);
}
```













