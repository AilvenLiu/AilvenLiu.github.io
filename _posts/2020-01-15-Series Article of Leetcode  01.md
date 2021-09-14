---
layout:     post
title:      Series Article of Leetcode Notes -- 01
subtitle:   Study Plan Algo 1        
date:       2021-09-14
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - leetcode      
    - c++     
    - algorithm            
---     

> leetcode 刷题笔记，Study Plan Algorithm 1 。     

## Day 1: 704 Binary Search 二分搜索        
[GitHub 连接](https://github.com/OUCliuxiang/leetcode/blob/master/StudyPlan/Algo1/day1/704_Binary_Search.cpp)       
Given an array of integers nums which is sorted in ascending order,      
and an integer target, write a function to search target in nums.       
If target exists, then return its index. Otherwise, return -1.    
 
You must write an algorithm with O(log n) runtime complexity.
 
Example 1:    
Input: nums = [-1,0,3,5,9,12], target = 9    
Output: 4     
Explanation: 9 exists in nums and its index is 4

Example 2:      
Input: nums = [-1,0,3,5,9,12], target = 2     
Output: -1      
Explanation: 2 does not exist in nums so return -1
  
Constraints:     
1 <= nums.length <= 104    
-104 < nums[i], target < 104
All the integers in nums are unique.   
nums is sorted in ascending order.

### My AC version:    
```c++           
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int length = nums.size();
        int rest = length;
        int upper = length, under = 0;
        int nowIndex = length / 2;
        int nowValue = nums[nowIndex];
        if ( nowValue == target) {
            return nowIndex;
        }
        rest /= 2;

        while (rest){
            if (target > nowValue){
                under = nowIndex;
                nowIndex = (nowIndex + upper)/2;
            }
            else{
                upper = nowIndex;
                nowIndex = (nowIndex + under) /2;
            }
            nowValue = nums[nowIndex];
            if (nowValue == target){
                return nowIndex;
            }
            rest/=2;
        }
        return -1;
    }
};
```       
Runtime: 40 ms, faster than 49.60% of C++ online submissions for Binary Search. 速度只战胜了 49.6% 的提交，不太行。        
Memory Usage: 27.4 MB, less than 99.80% of C++ online submissions for Binary Search. 空间占用战胜了 99.8% 的提交，这？           

极其朴素的二分思想，在进入循环之前进行一次判断，如果条件符合直接返回；通过 `while(rest /= 2)` 保证循环最大执行 log n 次；循环内根据目标数与当前值的大小判断交换 upper 上界和 under 下界，迭代循环；循环内进行判断没条件符合直接返回；如果循环内未返回而结束，必然说明不存在符合条件的数，返回 -1。     

## Official Version    
```c++    
int search_official(vector<int>& nums, int target) {
    int pivot, left = 0, right = nums.size() - 1;
    while (left <= right) {
        pivot = left + (right - left) /2;
        if (nums[pivot] == target) 
            return pivot;
        if (target < nums[pivot]) 
            right = pivot - 1;
        else left = pivot + 1;
        }
    return -1;
}
```     
出现在题目 Solution 中的代码，速度比我自己的代码稍快，主要是思想思路比我要简洁许多。      
上下界一定要写，这个解法直接给出一个 pivot 作为锚点（中点），中点更新为下界加上下界差的二分之一；其实很容易想，但我没想到。     
目标值大于锚点值，则上界不变，下界直接锚点加一。     
反之，下界不变，上界直接锚点减一。       
如果到了上下界相等时还找不到目标，下一次更新上下界，会出现上界比下界还小的情况，退出循环，这一点比较难想。      
但以界值作为判断条件，很简洁。