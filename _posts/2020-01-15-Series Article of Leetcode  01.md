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

## Day 1: Binary Search 二分搜索        
[GitHub 连接](https://github.com/OUCliuxiang/leetcode/blob/master/StudyPlan/Algo1/day1)       

### 704 Binary Search       
Given an array of integers nums which is sorted in ascending order, and an integer target, write a function to search target in nums. If target exists, then return its index. Otherwise, return -1.    
 
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

#### My AC version:    
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

#### Official solution    
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

### 278 First Bad Version         
You are a product manager and currently leading a team to develop a new product. Unfortunately, the latest version of your product fails the quality check. Since each version is developed based on the previous version, all the versions after a bad version are also bad.      

Suppose you have n versions [1, 2, ..., n] and you want to find out the first bad one, which causes all the following ones to be bad. You are given an API bool isBadVersion(version) which returns whether version is bad. Implement a function to find the first bad version.     
You should minimize the number of calls to the API.

 
Example 1:     
Input: n = 5, bad = 4     
Output: 4     
Explanation:     
call isBadVersion(3) -> false     
call isBadVersion(5) -> true     
call isBadVersion(4) -> true     
Then 4 is the first bad version.     

Example 2:     
Input: n = 1, bad = 1      
Output: 1     

Constraints:     
1 <= bad <= n <= 231 - 1      

#### My AC version      

```c++     
// The API isBadVersion is defined for you.     
// bool isBadVersion(int version);     

class Solution {
public:
    int firstBadVersion(int n) {
        int under=1, upper=n, target=n;
        
        while ( under <= upper){
            
            target = under + (upper - under) / 2;
            
            if ( target == 1 and isBadVersion( target) )
                return target;
            
            if ( isBadVersion( target)){
                if ( !isBadVersion( target - 1)){
                    return target;
                }
                else{
                    upper = target - 1;
                }
            }
            else{
                if ( isBadVersion( target + 1)){
                    return target + 1;
                }
                else{
                    under = target + 1;
                }
            }
        }
        return 0;
    }
};
```     
Runtime: 0 ms, faster than 100.00% of C++ online submissions for First Bad Version.       
Memory Usage: 6 MB, less than 21.66% of C++ online submissions for First Bad Version.      
其实这种简单题大家都差不多，相同的代码提交几次从 0 ms 到 5 ms ，5.8 MB 到 6.0 MB 不等，相差不大。    
这个题也是经典的二分搜索，比着上面一个题的葫芦画瓢就行。     
设置一个上下界，target 从 n/2 开始，更新上下界，直到找到符合条件的数或上下界矛盾为止。中间把 `n=1, target = 1` 这个情况拎出来单独讨论。       

#### Official Solution      
出现在 Solution 中的解法相当简洁，先贴代码：      
```c++     
public int firstBadVersion(int n) {
    int left = 1;
    int right = n;
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (isBadVersion(mid)) {
            right = mid;
        } else {
            left = mid + 1;
        }
    }
    return left;
}
```        
官方版本非常巧妙地利用了 c++ 整数除法向下取整的特性。考虑两种情况：     
1. 当 n=1，left = middle = right，不进入迭代，直接返回 left = 1 。       
2. 当 n>1，迭代终点的起始条件只能是 left + 1 = right，left = G, right=B；此时 mid = left， mid = G --> left += 1 = right 回到了条件 1，迭代结束，返回 left。     
非常巧妙。     

