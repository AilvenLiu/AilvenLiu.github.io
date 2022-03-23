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
快排基于分治策略：先保证大区间相对有序，在保证子区间内部有序。         
给定数组 `arr[]` 和区间 `[l, r]`，在该区间内随机找一个锚定点 `x = arr[idx]`。需要保证某一个下标点 i ，此点之前元素值均小于等于 x， 此点之后均大于等于 x 。这样就将数组划分成了两个区间：[l, i], [i, r]，其中前一个区间全部元素小于等于后一个区间全部元素，然后递归调用，对被划分的两个子区间排序，直到数组整体有序。           

#### 实现方案        

使用随机数随机选取 [l, r] 中的坐标点： `idx = rand() % (r-l+1) + l;`。            
初始化上下界下标为 `i = l-1, j = r+1;` 以方便 `do-while` 处理。             
`i, j` 分别向后向前逼近，直到发生交叉。期间如果存在 `arr[i] < x, arr[j] > x`，交换 `arr[i]` 和 `arr[j]` 。          
下标逼近过程停止的位置，要么 `i = j`, 要么 `i = j+1`。以 `j, j+1` 作为下一次细分区间的上下界，可以避免边界问题如下：       
对形如`[a, a]` 的只有两元素的子序列，下标逼近的结果必然是 `j=0, i=1;`，若以`i` 为下次细分数组的上界，则左子序列陷入循环处理 `[a, a]` 的死循环。          


#### 模板           

```c++
int arr[N];

void quick_sort(int l, int r){
    if (l >= r) return; // 区间没有或只有一个元素         
    int idx = rand() % (r - l + 1) + l;

    int x = arr[idx], i = l - 1, j = r + 1;

    while( i < j){
        do i++; while ( arr[i] < x);
        do j--; while ( arr[j] > x);
        if (i < j)  swap(arr[i], arr[j]);
    }

    quick_sort(l, j);
    quick_sort(j+1, r);
}
```

### 归并基础           

归并基于分治策略。和快排不同的是，归并排序需要先保证子区间有序，再通过辅助数组使得大区间整体有序。                

给定数组 `arr[]` 和上下界 `l, r` ， 设计 `merge_sort(l, r)` 函数，认为该函数可以完成对数组 `arr[]` 在 `[l, r]` 区间内的排序。于是将大数组对半分为 `[l, mid]`， 和 `[mid, r]`  两个子区间，并递归调用 `merge_sort` 完成子区间内部排序。将两个各自内部有序的子区间按照顺序放入辅助数组，然后再从辅助数组恢复到原数组，完成排序。                  


#### 实现方案              

给定数组 `arr[]` 和上下界 `l, r` ，并找到对半分的中点下标 `mid = l+r >> 1` 。此时 `mid` 一定是中间或中间偏左的点，于是划分子区间为`[l, mid], [mid+1, r]` 从而避免边界问题如下 ：          
对只有两元素的子区间 `[a, a]`， 有 `mid=l, r=l+1`，若以 `[l, mid-1], [mid, r]` 划分子区间，则右子区间将陷入 `[l, l+1]` 的死循环。            
对划分好的两个子区间递归调用 `merge_sort()`，并认为调用完之后子区间内部已经排好序。        
给定左子区间起始下标 `i=l`，右子区间起始下标 `j=mid+1`。当两者均未越界，while 循环同时向后逼近，依元素值，谁小谁先存入辅助数组 tmp。while 结束尚未到达边界的子区间按顺序全部存入 tmp。
             
此时 tmp 存储的就是 arr 数组 [l, r] 区间的有序排列，将之恢复到 arr[l, r] 区间，完成排序。         

#### 模板         

```c++
int arr[N], tmp[N];

void merge_sort(int l, int r){
    if (l >= r) return;     // 只有一个元素或没有元素          
    int mid = l+r >> 1;
    merge_sort(l, mid);
    merge_sort(mid+1, r);

    int i = l, j = mid+1, k = 0;
    while(i <= mid && j <= r){
        if (arr[i] < arr[j])    tmp[k++] = arr[i++];
        else                    tmp[k++] = arr[j++];
    }

    while( i <= mid)    tmp[k++] = arr[i++];
    while( j <= r)      tmp[k++] = arr[j++];

    i = l, k = 0;
    while(i <= r)   arr[i++] = tmp[k++];
}
```

### 归并排序思想的应用            

归并基于分治策略和思想，对于如下问题：            

> 给定一个长度为 n 的整数数列，请你计算数列中的逆序对的数量。               
> 逆序对的定义如下：对于数列的第 i 个和第 j 个元素，如果满足 i<j 且 a[i]>a[j]，则其为一个逆序对；否则不是。            

若暴力搜索，则必然 TLE。于是使用归并思想：               
先将数组对半分为左右两个子区间，则大数组中逆序对的数量等于 ① 两个子区间内部逆序对的数量加上  ② i 在左子区间、j 在右子区间的逆序对的数量。我们假设调用 `merge_sort(l, r)` 会返回 `[l, r]` 区间内逆序对的数量，则，只需要两个子区间内部有序，第 ② 种情况易得：        
若 `arr[i] > arr[j]`，则 `[i, mid]` 区间内所有元素大于 `arr[j]`，则逆序对数量 `+= mid+1-i`。                

#### 代码            

如判断逆序对总数量超过了可 Integer 可表示范围， 不妨使用 `typedef long long LL;` 类型变量存储逆序对数量。          

```c++
int arr[N], tmp[N];

int merge_sort(l, r){
    if (l <= r) return 0;
    int mid = l+r >> 1;
    int count = merge_sort(l, mid) + merge_sort(mid+1, r);
    int i = l, j = mid+1, k = 0;
    while(i <= mid && j <= r){
        if (arr[i] <= arr[j])    tmp[k++] = arr[i++];
        else {tmp[k++] = arr[j++]; count += mid+1-i;}
    }

    while(i <= mid)     tmp[k++] = arr[i++];
    while(j <= r)       tmp[k++] = arr[j++];
    i=l, k = 0;
    while(i <= r)       arr[i++] = tmp[k++];

    return count;
}
```             

### 二维数组排序                    

需求是按照第二维度只有两个数的二维数组前一个数排序， 和一维情况没什么差别：          
```c++
void quick_sort(int l, int r){
    if (l >= r) return;
    
    int idx = (int)( rand() % (r-l+1) + l);
    int x = partitions[idx][0];
    
    int i = l-1, j = r+1;
    while(i < j){
        do i++; while ( partitions[i][0] < x);
        do j--; while ( partitions[j][0] > x);
        if( i < j) {
            int tmpl = partitions[i][0], 
                tmpr = partitions[i][1];
            partitions[i][0] = partitions[j][0];
            partitions[i][1] = partitions[j][1];
            
            partitions[j][0] = tmpl;
            partitions[j][1] = tmpr;
        }
    }
    
    quick_sort(l, j);
    quick_sort(j+1, r);
}

void merge_sort(int l, int r){
    if (l >= r) return;
    
    int mid = l+r >> 1;
    merge_sort(l, mid);
    merge_sort(mid+1, r);
    
    int i = l, j = mid + 1, k = 0;
    while (i <= mid && j <= r){
        if (partitions[i][0] < partitions[j][0]){
            tmp[k][0] = partitions[i][0];
            tmp[k][1] = partitions[i][1];
            i ++;
        }
        else {
            tmp[k][0] = partitions[j][0];
            tmp[k][1] = partitions[j][1];
            j ++;
        }
        k++;
    }
    while (i <= mid){
        tmp[k][0] = partitions[i][0];
        tmp[k][1] = partitions[i][1];
        i++, k++;
    }
    while (j <= r){
        tmp[k][0] = partitions[j][0];
        tmp[k][1] = partitions[j][1];
        j++, k++;
    }
    
    i = l, k = 0;
    while(i <= r){
        partitions[i][0] = tmp[k][0];
        partitions[i][1] = tmp[k][1];
        i++, k++;
    }
}
```
















