---
layout:     post
title:      Series Article of Leetcode Notes -- 03
subtitle:   Study Plan Algo 2 Day 1-7      
date:       2021-10-06
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - leetcode      
    - c++     
    - algorithm            
---     

> leetcode 刷题笔记，Study Plan Algorithm 2, Day 1 -- Day 7 。      

## Day 1 Binary Search            
[GitHub 连接](https://github.com/OUCliuxiang/leetcode/blob/master/StudyPlan/Algo2/day01)          

### 34. Find First and Last Position of Element in Sorted Array           

Given an array of integers nums sorted in ascending order, find the starting and ending position of a given target value.          
If target is not found in the array, return [-1, -1].          
You must write an algorithm with O(log n) runtime complexity.           

Example 1:         
Input: nums = [5,7,7,8,8,10], target = 8             
Output: [3,4]        

Example 2:         
Input: nums = [5,7,7,8,8,10], target = 6            
Output: [-1,-1]           

Example 3:          
Input: nums = [], target = 0            
Output: [-1,-1]           

Constraints:
* 0 <= nums.length <= 105           
* -109 <= nums[i] <= 109            
* nums is a non-decreasing array.            
* -109 <= target <= 109

#### My AC Version           
```c++     
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        vector<int> res(2, -1);
        if (nums.size() == 0) return res;
        int left = 0, right = nums.size()-1, middle;
        while(left <= right){
            middle = left + (right-left)/2;
            if (target == nums[middle])
                break;
            if (target > nums[middle])
                left = middle + 1;
            else 
                right = middle - 1;
        }
        if (target != nums[middle])
            return res;
        left = middle;  right = middle;
        while(left >= 0 && nums[left] == target)
            left --;
        res[0] = left + 1;
        while(right < nums.size() && nums[right] == target)
            right ++;
        res[1] = right - 1;
        return res;
    }
};
```       
Runtime: 4 ms, faster than **95.68%** of C++ online submissions for Find First and Last Position of Element in Sorted Array.
Memory Usage: 13.6 MB, less than 85.13% of C++ online submissions for Find First and Last Position of Element in Sorted Array.       

比较中庸的二分搜索，空间表现偶尔（根据submit而随机）不太好。            


#### Discuss solution          

讨论区比较出众，也比较符合我的预期的解法是使用 `lower_bound` / `upper_bound` 库方法一步求解：         
```c++     
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        int lo = lower_bound(nums.begin(), nums.end(), target) 
            - nums.begin();
        if (lo == nums.size() || nums[lo] != target)
            return {-1, -1};
        int hi = upper_bound(nums.begin(), nums.end(), target) 
            - nums.begin();
        return {lo, hi-1};
    }
};
```         
Runtime: 8 ms, faster than 69.85% of C++ online submissions for Find First and Last Position of Element in Sorted Array.         
Memory Usage: 13.7 MB, less than 17.64% of C++ online submissions for Find First and Last Position of Element in Sorted Array.           
属于前任造轮子后人乘凉，但是这个轮子的时间空间表现，一言难尽。         

### 33. Search in Rotated Sorted Array         
There is an integer array nums sorted in ascending order (with distinct values).              
Prior to being passed to your function, nums is possibly rotated at an unknown pivot index k (1 <= k < nums.length) such that the resulting array is [nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]] (0-indexed). For example, [0,1,2,4,5,6,7] might be rotated at pivot index 3 and become [4,5,6,7,0,1,2].            
Given the array nums after the possible rotation and an integer target, return the index of target if it is in nums, or -1 if it is not in nums.        
You must write an algorithm with O(log n) runtime complexity.

Example 1:          
Input: nums = [4,5,6,7,0,1,2], target = 0           
Output: 4           

Example 2:          
Input: nums = [4,5,6,7,0,1,2], target = 3             
Output: -1            

Example 3:            
Input: nums = [1], target = 0           
Output: -1            

Constraints:                
* 1 <= nums.length <= 5000            
* -104 <= nums[i] <= 104       
* All values of nums are unique.           
* nums is an ascending array that is possibly rotated.        
* -104 <= target <= 104

#### My AC Version         
```c++       
class Solution {
public:
    int search(vector<int>& nums, int target) {
        vector<int>::iterator it = find(nums.begin(), nums.end(), target);
        return it == nums.end() ? -1 : it - nums.begin();
    }
};
```        
Runtime: 8 ms, faster than 20.56% of C++ online submissions for Search in Rotated Sorted Array.       
Memory Usage: 11.1 MB, less than 75.41% of C++ online submissions for Search in Rotated Sorted Array.       
按照非排序区间进行调用线性查找库函数 find(begin, end, tar)，可得答案。时间空间表现差了些。          

也可以使用库函数 min_element() 寻找出最小元素，然后将偏移后的有序数组重新排序为真·有序数组，从而使用适用于有序数组的有 O(logn) 复杂度的 binary_search 和 lower_bound() ：           
```c++ 
class Solution {
public:
    int search(vector<int>& nums, int target) {
        vector<int>::iterator it = min_element(nums.begin(), nums.end());
        int shift = it - nums.begin();
        for (int i = 0; i < shift; i++){
            nums.emplace_back(nums[0]);
            nums.erase(nums.begin());
        }
        nums.erase(max_element(nums.begin(), nums.end())+1, nums.end());
        if (!binary_search(nums.begin(), nums.end(), target))
            return -1;
        
        return (lower_bound(nums.begin(), nums.end(), target) - nums.begin() + shift)%nums.size();
    }
};
```         
Runtime: 8 ms, faster than 20.56% of C++ online submissions for Search in Rotated Sorted Array.          
Memory Usage: 11.1 MB, less than 29.52% of C++ online submissions for Search in Rotated Sorted Array.      
时间空间表现也不怎么样。还是继续看讨论区。           

#### Discuss solution       
```c++           
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int left = 0, right = nums.size()-1, middle;
        while(left <= right){
            middle = (left + right)/2;
            if (nums[middle] == target)
                return middle;
            if (nums[left] <= nums[middle]){
                if (target >= nums[left] && target < nums[middle])
                    right = middle-1;
                else
                    left = middle + 1;
            }
            else{
                if (target > nums[middle] && target <= nums[right])
                    left = middle + 1;
                else
                    right = middle - 1;
            }
        }
        return -1;
    }
};
```          
Runtime: 4 ms, faster than **72.45%** of C++ online submissions for Search in Rotated Sorted Array.         
Memory Usage: 11.2 MB, less than 29.52% of C++ online submissions for Search in Rotated Sorted Array.      
代码比较好理解，纯手工实现了在有偏移有序数组中的二分搜索，时间表现有较大进步，空间表现还是不行，这样吧，留点遗憾。简单而言，就是在判断完 middle 左侧数组是否为有序后，加一步对 target 是否在该序列中的判断。       
   