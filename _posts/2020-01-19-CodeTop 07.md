---
layout:     post
title:      Code Top -- 07 最大子数组和
subtitle:   最大子数组和    
date:       2022-03-29
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Algorithm     
--- 

> from CodeTop, Leetcode, [53. 最大子数组和](https://leetcode-cn.com/problems/maximum-subarray/)。                       
> 动态规划。                    


给你一个整数数组 nums ，请你找出一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。         
子数组 是数组中的一个连续部分。              


## 思路             
动态规划典型题，状态转移方程要清楚。             

**注意**，状态转移方程：            
`f(i) = max(nums[i], nums[i] + f(i-1))` ，从前往后遍历，以 nums[i] 结尾的 f(i) 则必然是子数组连续的。         


## 代码                                        
```c++
int maxSubArray(vector<int>& nums){
    int a[nums.size()];
    a[0] = nums[0];
    int res = a[0];
    for (int i = 1; i < nums.size(); i++){
        a[i] = max(nums[i], a[i-1] + nums[i]);
        res = max(a[i], res);
    }

    return res;
}
```
