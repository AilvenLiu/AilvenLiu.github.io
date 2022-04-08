---
layout:     post
title:      Series Article of Algorithm and Data Structure -- 08 
subtitle:   全排列问题中的递归回溯思想    
date:       2022-04-08
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Algorithm      
--- 

> from LeetCode , [46. 全排列](https://leetcode-cn.com/problems/permutations/submissions/)          
> 一道非常经典的递归回溯问题，可以当做模板背下来。                

题目如下：      
给定一个不含重复数字的数组 nums ，返回其 所有可能的全排列 。你可以 按任意顺序 返回答案。         

> 示例 1：         
> 输入：nums = [1,2,3]            
> 输出：[[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]         
          
### 题目解读               

将结果数组存储到全局变量，将路径数组记录为全局变量，使用一个全局变量布尔数组记录某个位置的元素是否使用过。             

递归调用选择从零到最后，每个位置上的可以放入的数。递归起点是位置 0 ，在主函数中调用。放完一个位置要将该位置的 bool 值记录为不可用，然后调用递归函数选择下一位置要放的数。          

**注意回溯（恢复现场）**
递归调用完，要恢复现场，也即，递归函数调用前将放在该位置数对应的 bool 值设置为了不可用，所以下面层的递归调用中不再允许选择该元素。现在递归完全结束，也即以 “该元素不再可用” 为前提条件的递归结束，该元素位置的 bool 应当恢复为可用状态。称为恢复现场。           

```c++
public:
    vector<vector<int>> ans;    // 记录结果。                 
    vector<int> path;           // 记录路径。                
    vector<bool> used;          // 记录元素是否使用过。          

    vector<vector<int>> permute(vector<int>& nums){
        path = vector<int>(nums.size());
        used = vector<bool>(nums.size());
        fill(used.begin(), used.end(), false);

        dfs(nums, 0);   // 递归入口，第一个位置可以放什么数？                

        return ans;
    }
private:
    void dfs(vector<int>& nums, int u){ // 递归函数，第 u 个位置可以放什么数？       
        if (u == nums.size()){  
            // 走到最后一个位置，全部元素已添加完毕，可以添加到结果集中。              
            ans.push_back(path);
            return;
        }

        for (int i = 0; i < nums.size(); i++){  //遍历所有元素              
            if (!used[i]){  //  如果第 i 个元素没有使用过，可以加入到 u 位置。         
                used[i] = true; // 变更状态为使用过。               
                path[u] = nums[i];  //  把第 i 个元素放到 u 位置。           
                dfs(nums, u+1);     // 递归找 u+1 位置能放什么元素。         
                used[i] = false;    
                // 恢复现场，相同位置将遍历到下一个元素，也即该元素（第 i 个）          
                // 已不再放置到 u 位置，恢复到未使用状态。            
            }
        }
    }
```
