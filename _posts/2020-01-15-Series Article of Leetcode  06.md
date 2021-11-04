---
layout:     post
title:      Series Article of Leetcode Notes -- 05
subtitle:   Study Plan DS I 01-07      
date:       2021-11-03
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - leetcode      
    - c++     
    - data structure            
---     

> leetcode 刷题笔记，Study Plan Data Structure 1, Day 1 -- Day 7 。说好的数据结构呢，又一堆算法题。      
> 对了，从 DS 开始转战力扣中文站，虽然界面难看，加载太慢，但讨论区似乎比全球站更贴近人类的思想，没有那么多奇技淫巧。        

## 第 1 天 数组           

### 53. 最大子序和           
给定一个整数数组 nums ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。         

示例 1：       
输入：nums = [-2,1,-3,4,-1,2,1,-5,4]         
输出：6         
解释：连续子数组 [4,-1,2,1] 的和最大，为 6 。         

示例 2：       
输入：nums = [1]      
输出：1       

示例 3：       
输入：nums = [0]      
输出：0       

示例 4：        
输入：nums = [-1]       
输出：-1          

示例 5：           
输入：nums = [-100000]      
输出：-100000           

提示：         
* 1 <= nums.length <= 105         
* -104 <= nums[i] <= 104       

#### My AC Version     
```c++     
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        if (nums.size() == 1) return nums.back();
        int len = nums.size();
        int dp[len]; 
        memset(dp, 0, sizeof(int)*len);
        dp[0] = nums[0];
        int res = dp[0];
        for(int i = 1; i < len; i++){
            dp[i] = dp[i-1] < 0 ? nums[i] : dp[i-1] + nums[i] ;
            res = max(res, dp[i]);
        }
        return res;
    }
};
```       
执行用时：104 ms, 在所有 C++ 提交中击败了12.92% 的用户      
内存消耗：66.5 MB, 在所有 C++ 提交中击败了8.94% 的用户       

这题，明显用动态规划去做。先考虑对数组长度为 1 的特殊情况，直接返回 nums.back()。       
构造长度为 len = nums.size() 的 dp 数组，dp[i] 代表直到 nums 以 idx = i 为尾部的连续子序列和；同时维护一个 res 变量存储最大连续子序列和。 dp 数组的更新策略（状态转移）则为：     


## 第 2 天 数组       

### 1. 两数之和

给定一个整数数组 nums 和一个整数目标值 target，请你在该数组中找出 和为目标值 target  的那 两个 整数，并返回它们的数组下标。      
你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。     
你可以按任意顺序返回答案。       

示例 1：     
输入：nums = [2,7,11,15], target = 9        
输出：[0,1]        
解释：因为 nums[0] + nums[1] == 9 ，返回 [0, 1] 。         

示例 2：         
输入：nums = [3,2,4], target = 6           
输出：[1,2]          

示例 3：       
输入：nums = [3,3], target = 6         
输出：[0,1]          

提示：       
* 2 <= nums.length <= 104         
* -109 <= nums[i] <= 109         
* -109 <= target <= 109         
* 只会存在一个有效答案

#### My AC Version        
```c++      
class Solution {
public:
    std::vector<int> twoSum(std::vector<int>& nums, int target) {
        std::unordered_set<int> pool;
        for (int v: nums) pool.emplace(v);
        for (int i = 0; i < nums.size(); i++){
            if(nums[i] == target - nums[i]){
                if (std::find(nums.begin()+i+1, nums.end(), target-nums[i]) != nums.end())
                    return {i, (int)(std::find(nums.begin()+i+1, nums.end(), target-nums[i]) - nums.begin())};
            }else{
                pool.erase(nums[i]);
                if (pool.find(target-nums[i])!= pool.end())
                    return {i, (int)(std::find(nums.begin(), nums.end(), target-nums[i])-nums.begin())};
            }
        }
        return {0,0};
    }
};
```     
执行用时：12 ms, 在所有 C++ 提交中击败了73.30% 的用户      
内存消耗：11.7 MB, 在所有 C++ 提交中击败了7.76% 的用户         

经典题目了，这题用暴力罚不会超时，但不应该使用暴力：使用哈希结构的 `unordered_set` 进行搜索可以将时间复杂度降到 O(n)。     

把所有的元素加入到 unordered_set 中，开始遍历 nums，分两种情况讨论：    
1. target - nums[i] == nums[i]。此时从 i+1 寻找值为 nums[i] 的元素，如果存在，直接返回；      
2. 否则，先从 unordered_set 中 erase 删掉值为 nums[i] 的元素，然后查找值为 target - nums[i] 的元素。如果找到，直接返回。       

如果遍历完全部元素而仍没有返回，则不存在这样两个元素，返回 {0,0}。        

