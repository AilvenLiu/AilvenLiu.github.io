---
layout:     post
title:      Series Article of Leetcode Notes -- 09
subtitle:   Study Plan DS II 01-07      
date:       2021-11-16
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - leetcode      
    - c++      
    - data structure      
---     

> leetcode 刷题笔记，Study Plan Data Structure 2, Day 1 -- Day 7 。         

## 第 1 天 数组           

### 136. 只出现一次的数字
给定一个非空整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。        

说明：         
你的算法应该具有线性时间复杂度。 你可以不使用额外空间来实现吗？          

示例 1:       
输入: [2,2,1]       
输出: 1           
示例 2:         

输入: [4,1,2,1,2]         
输出: 4          

#### Thought & AC          
排好序后从零开始遍历（idx 始于 0 的偶数位），如果某个元素和其后面一位相等，则额外进行一次 i++，也就是总共向后走两位移到下一个偶数位；直到不相等，返回该值。       
```c++        
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        if (nums.size() == 1) return nums.at(0);
        sort(nums.begin(), nums.end());
        for (int i = 0; i < nums.size()-1; i++){
            if (nums.at(i) == nums.at(i+1))
                i+=1;
            else return nums.at(i);
        }
        return nums[nums.size()-1];
    }
};
```        
执行用时： 24 ms, 在所有 C++ 提交中击败了 23.82% 的用户          
内存消耗： 16.4 MB, 在所有 C++ 提交中击败了 77.16% 的用户       

### 169. 多数元素     
给定一个大小为 n 的数组，找到其中的多数元素。多数元素是指在数组中出现次数 大于 ⌊ n/2 ⌋ 的元素。      
你可以假设数组是非空的，并且给定的数组总是存在多数元素。       

示例 1：       
输入：[3,2,3]      
输出：3      
示例 2：      

输入：[2,2,1,1,1,2,2]      
输出：2      

进阶：         
尝试设计时间复杂度为 O(n)、空间复杂度为 O(1) 的算法解决此问题。        

#### Thought & AC           
则排好序后的数组的 idx=[n/2] 处必是多数元素。          
```c++         
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        return nums.at(nums.size()/2);
    }
};
```        
执行用时： 16 ms, 在所有 C++ 提交中击败了 73.03% 的用户      
内存消耗： 19.2 MB, 在所有 C++ 提交中击败了 20.66% 的用户        

### 15. 三数之和        
给你一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？请你找出所有和为 0 且不重复的三元组。        
注意：答案中不可以包含重复的三元组。      

示例 1：       
输入：nums = [-1,0,1,2,-1,-4]         
输出：[[-1,-1,2],[-1,0,1]]    

示例 2：       
输入：nums = []       
输出：[]       

示例 3：       
输入：nums = [0]       
输出：[]       
 
提示：        
* 0 <= nums.length <= 3000        
* -105 <= nums[i] <= 105

#### Thought         
本质上就是一个两数和：遍历 idx = 0 ~ nums.size()-3， 以 -nums[idx] 作为 target 查找两数和。则可以通过有序数组的 while(left < right) 高效查找。       
需要注意的是本题存在重复元素，则查找过程需要避开：如果当前元素和查找过的上一元素值相等，continue  或 while 走到当前行走方向第一个不相等元素；当然，while 的时候需要限定 idx 范围，否则会遇到 index out of range 错误。           

#### My AC Version         
```c++    
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> res;
        if (nums.size() < 3) return res;
        sort(nums.begin(), nums.end());
        for (int i = 0; i < nums.size()-2; i++){
            if (i != 0 && nums.at(i) == nums.at(i-1)) continue;
            twoSum(nums, res, i, -nums.at(i));
        }
        return res;
    }
private:
    void twoSum(vector<int>& nums, vector<vector<int>>& res, int begin, int target){
        int i = begin+1, j = nums.size()-1;
        while(i<j){
            int tmp = nums.at(i) + nums.at(j);
            if (tmp == target){
                res.emplace_back(vector<int> {nums.at(begin), nums.at(i), nums.at(j)});
                i++; while(i<nums.size() && nums.at(i) == nums.at(i-1)) i++;
                j--; while(j>=0 && nums.at(j) == nums.at(j+1)) j--;
            }
            else if (tmp < target) i++;
            else if (tmp > target) j--;
        }
    }
};
```