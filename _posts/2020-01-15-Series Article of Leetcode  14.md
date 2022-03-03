---
layout:     post
title:      Series Article of Leetcode Notes -- 14
subtitle:   排序算法总结      
date:       2021-12-10
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:     
    - c++      
    - algorithm      
---     

> 总结常见的排序算法。         

## 基础        
### 分类
排序算法可分稳定和不稳定两类：          
1. **稳定排序：**  如果 a 原本在 b 的前面，且 a == b，排序之后 a 仍然在 b 的前面，则为稳定排序。         
2. **非稳定排序：**  如果 a 原本在 b 的前面，且 a == b，排序之后 a 可能不在 b 的前面，则为非稳定排序。

本篇博客将对适用于数组的常见稳定排序算法（冒泡排序 bubble，插入排序 insert ，归并排序 merge）和非稳定排序算法（选择排序 select，快速排序 quick，希尔排序 shell，堆排序 heap） 进行总结。               

### 复杂度比较             
|算法|平均时间复杂度|平均空间复杂度|稳定性|
|:---|:---|:---|:---|
|冒泡|O(n^2)|O(1)|稳定|
|插入|O(n^2)|O(1)|稳定|
|归并|O(nlogn)|O(n)|稳定|
|选择|O(n^2)|O(1)|不稳定|
|希尔|O(nlogn)|O(1)|不稳定|
|快排|O(nlogn)|O(logn)|不稳定|
|堆排序|O(nlogn)|O(1)|不稳定|        

## 稳定排序         
1. 比较相邻的元素。如果第一个比第二个大，就交换他们两个。          
2. 对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对。这步做完后，最后的元素会是最大的数。          
3. 针对所有的元素重复以上的步骤，除了最后一个。        
4. 持续每次对越来越少的元素重复上面的步骤，直到没有任何一对数字需要比较。 

```c++
void bubbleSort(vector<int>& nums){
    int len = nums.size();
    for (int i = 0; i < len-1; ++i){
        for (int j = 0;  j< len-1-i; ++j){
            if (nums[j] > nums[j+1])
                swap(nums[j], nums[j+1]);
        }
    }
}
```

考虑一种情况，在某一趟冒泡过程中，没有发生元素交换。这即意味着当前数组整体已经是有序的了，此时可以结束冒泡，而没有继续比较下去的必要了。            
```c++        
void bubbleSort(vector<int>& nums){
    int len = nums.size();
    for (int i = 0; i < len-1; ++i){
        bool flag = false;
        for (int j = 0; j < len-i-1; ++j){
            if (nums[j] > nums[j+1]){
                flag = true;
                swap(nums[j], nums[j+1]);
            }
        }
        if (!flag) break;
    }
}
``` 

### 插入排序           
1. 从第一个元素开始，该元素可以认为已经被排序             
2. 取出已排序序列的下一个元素，在已经排序的元素序列中从后向前扫描           
3. 如果该元素（已排序）大于新元素，将该元素移到下一位置           
4. 重复步骤3，直到找到已排序的元素小于或者等于新元素的位置         
5. 将新元素插入到该位置后         
6. 重复步骤2~5           

```c++
void insert(vector<int>& nums){
    int len = nums.size();
    for (int i = 1; i < len; ++i){
        if (nums[i] < nums[i-1]){ 
            int x = nums[i];
            int j = i-1;
            while(j>=0 && x < nums[j]){ 
                nums[j+1] = nums[j]; // 元素依次后移              
                --j;
            }
            nums[j+1] = x;
        }
        // 如果不小于，意味着新元素和他前面的有序序列仍然有序，不用动       
    }
}
```    

### 归并排序        
归并排序是建立在归并操作上的一种有效的排序算法。该算法是分治法（Divide and Conquer）的一个非常典型的应用。将已有序的子序列合并同时排序，可以得到一个完全有序的序列；即先使每个子序列有序，再使子序列段间有序。将两个有序表合并成一个有序表，称为2-路归并。        

```c++        
template<typename T>
void mergeSort(vector<int>& nums)
```