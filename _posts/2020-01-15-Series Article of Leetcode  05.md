---
layout:     post
title:      Series Article of Leetcode Notes -- 05
subtitle:   Study Plan Algo 2 12-18 动态规划专题      
date:       2021-10-29
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - leetcode      
    - c++     
    - algorithm            
---     

> leetcode 刷题笔记，Study Plan Algorithm 2, Day 12 -- Day 18 ，这五天的题全是动态规划，给爷刷吐了。     

## Day 12 Dynamic Programming           
[GitHub 连接](https://github.com/OUCliuxiang/leetcode/blob/master/StudyPlan/Algo2/day12)          

### 213. House Robber II         
You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed. All houses at this place are arranged in a circle. That means the first house is the neighbor of the last one. Meanwhile, adjacent houses have a security system connected, and it will automatically contact the police if two adjacent houses were broken into on the same night.       
Given an integer array nums representing the amount of money of each house, return the maximum amount of money you can rob tonight without alerting the police.        

Example 1:        
Input: nums = [2,3,2]         
Output: 3        
Explanation: You cannot rob house 1 (money = 2) and then rob house 3 (money = 2), because they are adjacent houses.          

Example 2:       
Input: nums = [1,2,3,1]       
Output: 4         
Explanation: Rob house 1 (money = 1) and then rob house 3 (money = 3).     
Total amount you can rob = 1 + 3 = 4.     

Example 3:      
Input: nums = [1,2,3]       
Output: 3      

Constraints:         
* 1 <= nums.length <= 100                 
* 0 <= nums[i] <= 1000        

这道题是 [198. House Robber](https://leetcode.com/problems/house-robber/) 偷房子问题添加首尾不能相连的新约束的进阶，首先回顾偷房子问题的动态规划解：            

#### 198. House Robber           
```c++       
class Solution {
public:
    int rob(vector<int>& nums) {
        if (nums.size() == 1) return nums[0];
        if (nums.size() == 2) return max(nums[0], nums[1]);
        int len = nums.size();
        int pre = nums[0], cur = nums[1];        
        for (int i = 2; i < len; i++){
            int tmp = cur;
            cur = max(cur, pre+nums[i]);
            pre = max(tmp, pre);
        }
        return cur;
    }
};
```       
思路是用两个值分别存储 **直到** 当前（0 到 当前）房子，和 **直到** 上一 （0 到当前 -1）房子的能偷到的最大值，并从第三个房子开始，每一步更新，直到最后一个房子。      
而首尾不能相连的约束条件，相当于约束了第一间房和最后一间房，**最多** 只能偷一间。于是，我们给小偷两个选择，从第一间偷到倒数第二间、从第二间偷到最后一间，然后在这两个选择的返回值中选择最大的哪一个，就是我们需要的答案。至于，第一间和最后一间，你是偷一间还是一间都不偷，是小偷自己的事，我们不要去关心，千万不要去关心。      
#### My AC Version           
```c++       
class Solution {
public:
    int rob(vector<int>& nums) {
        if (nums.size() == 1) return nums[0];
        if (nums.size() == 2) return max(nums[0], nums[1]);
        if (nums.size() == 3) return max(max(nums[0], nums[1]), nums[2]);
        
        int len = nums.size();
        
        int pre = nums[0], cur = nums[1];        
        for (int i = 2; i < len-1; i++){
            int tmp = cur;
            cur = max(cur, pre+nums[i]);
            pre = max(tmp, pre);
        }
        
        int pre2 = nums[1], cur2 = nums[2];        
        for (int i = 3; i < len; i++){
            int tmp = cur2;
            cur2 = max(cur2, pre2+nums[i]);
            pre2 = max(tmp, pre2);
        }
        
        return max(cur, cur2);
    }
};
```      
Runtime: 4 ms, faster than 41.17% of C++ online submissions for House Robber II.      
Memory Usage: 7.9 MB, less than 57.87% of C++ online submissions for House Robber II.          

### 55. Jump Game      
You are given an integer array nums. You are initially positioned at the array's first index, and each element in the array represents your maximum jump length at that position.         
Return true if you can reach the last index, or false otherwise.      

Example 1:          
Input: nums = [2,3,1,1,4]        
Output: true         
Explanation: Jump 1 step from index 0 to 1, then 3 steps to the last index.        

Example 2:       
Input: nums = [3,2,1,0,4]          
Output: false            
Explanation: You will always arrive at index 3 no matter what. Its maximum jump length is 0, which makes it impossible to reach the last index.           

Constraints:           
* 1 <= nums.length <= 104           
* 0 <= nums[i] <= 105

#### My AC Version         
```c++         
class Solution {
public:
    bool canJump(vector<int>& nums) {
        int len = nums.size(), dis = 0;
        for(int i = 0; i <= dis; i ++){
            dis = max(dis, i+nums[i]);
            if (dis >= len-1) return true;
        }
        return false;
    }
};
```       
Runtime: 96 ms, faster than 32.84% of C++ online submissions for Jump Game.        
Memory Usage: 48.4 MB, less than 48.88% of C++ online submissions for Jump Game.        
动态规划类型题难的就是题目抽象和提取：状态是什么，转移方程是什么。其中最重要的还是 **状态** 的确定。对本题而言，状态应当为 *到当前步（index）能达到的最远距离* 。确定好状态之后，转移方程呼之欲出随之确定：遍历每一步更新（转移）最远距离（状态） dis 为 current value 和当前步（idx）加上当前步能达到的最远距离（nums[idx]） 的最大值。直到 dis >= len(nums)-1，认为可以达到终点，返回 true。否则，再循环之外给出默认返回值 false，意即为一旦完整走完了循环还没有返回，就达不到终点。       

## Day 13 Dynamic Programming           
[GitHub 连接](https://github.com/OUCliuxiang/leetcode/blob/master/StudyPlan/Algo2/day13)          

### 45. Jump Game II        
Given an array of non-negative integers nums, you are initially positioned at the first index of the array.           
Each element in the array represents your maximum jump length at that position.        
Your goal is to reach the last index in the minimum number of jumps.       
You can assume that you can always reach the last index.        

Example 1:       
Input: nums = [2,3,1,1,4]         
Output: 2        
Explanation: The minimum number of jumps to reach the last index is 2. Jump 1 step from index 0 to 1, then 3 steps to the last index.       

Example 2:         
Input: nums = [2,3,0,1,4]         
Output: 2         

Constraints:          
* 1 <= nums.length <= 104          
* 0 <= nums[i] <= 1000       

上一题的进阶，在一定可以到达终点的情况下，求出最少需要的步数。       

#### My AC Version        
```c++       
class Solution {
public:
    int jump(vector<int>& nums) {
        int len = nums.size();
        if (len <= 1) return 0;
        
        int step = 1, i = 0, maxDis = nums[0], curDis = nums[0];
        while(i <= curDis && i < len){
            if( i == len -1) return step;
            maxDis = max(maxDis, i + nums[i]);
            if(i == curDis){
                curDis = maxDis;
                step ++;
            }
            i++;
        }
        return step;
    }
};
```     

Runtime: 8 ms, faster than 98.55% of C++ online  submissions for Jump Game II.       
Memory Usage: 16.4 MB, less than 42.07% of C++ online submissions for Jump Game II.    

这一题比上一题要复杂一些，但本质上也还是动态规划解的两点：找出状态，找出状态转移方程。         
复杂就复杂在，我们希望每次跳跃，都要达到当前次数下的最远值。于是这个题的状态应该有两个，两个状态之间还要有交互。先看两个状态：       
1. 状态 1，**直到** 当前格子，最远可达到的距离 maxDis;         
2. 状态 2，**站在** 当前格子，当前可达到的距离 curDis。       

于是，转台转移为：状态 1 最远可达距离和上一题一样每一步更新，状态 2 当前可达距离当且仅当 i 走到 现在的当前距离的时候更新为最远可达距离。解释为：     
在每一个格子（基准格子），尽力往前跳，直到跳到这个格子能到的最远距离（中间每跳一步除了更新状态1 直到当前格可达最远距离外，还要判断跳到当前格是否到达终点，是的话直接返回步数，不再继续进行）。现在，我们从当前步的基准格子跳到了基准格子可达的最远距离，也找到了从基准格子直到当前格子可跳到的最远距离。注意，我们不关注到底是从那个格子能跳到状态 1 的最远距离，因为都一样，步数都要加一。此时，循环还没有结束、还没有到达终点，更新当前可达距离为直到当前格子的最远可达距离。步数加一继续往前跳。      

