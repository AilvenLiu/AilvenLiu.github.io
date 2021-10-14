---
layout:     post
title:      Series Article of Leetcode Notes -- 04
subtitle:   Study Plan Algo 2 Day 8-14      
date:       2021-10-13
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - leetcode      
    - c++     
    - algorithm            
---     

> leetcode 刷题笔记，Study Plan Algorithm 2, Day 8 -- Day 14 。      

## Day 8 Breadth-First Search / Depth-First Search             
[GitHub 连接](https://github.com/OUCliuxiang/leetcode/blob/master/StudyPlan/Algo2/day08)          

### 1091. Shortest Path in Binary Matrix           

Given an n x n binary matrix grid, return the length of the shortest clear path in the matrix. If there is no clear path, return -1.         
A clear path in a binary matrix is a path from the top-left cell (i.e., (0, 0)) to the bottom-right cell (i.e., (n - 1, n - 1)) such that:         
* All the visited cells of the path are 0.          
* All the adjacent cells of the path are 8-directionally connected (i.e., they are different and they share an edge or a corner).      

The length of a clear path is the number of visited cells of this path.          

Example 1:         
Input: grid = [[0,1],[1,0]]          
Output: 2           

Example 2:         
Input: grid = [[0,0,0],[1,1,0],[1,1,0]]         
Output: 4            

Example 3:        
Input: grid = [[1,0,0],[1,1,0],[1,1,0]]         
Output: -1             


#### MY AC Version           
```c++          
class Solution {
public:
    int shortestPathBinaryMatrix(vector<vector<int>>& grid) {
        int n = grid.size();
        if (grid[0][0] != 0 || grid[n-1][n-1] != 0 ) return -1;
        if (n == 1) return 1;
        
        queue<pair<int,int>> q;
        grid[0][0] ++;
        q.push(make_pair(0,0));
        while(!q.empty()){
            pair<int, int> pos = q.front();
            q.pop();
            int row = get<0>(pos);
            int col = get<1>(pos);
            for (int i = row-1; i < row+2; i++){
                for (int j = col-1; j < col+2; j++){
                    if (i>=0 && j>=0 && i<n && j<n && grid[i][j] == 0){
                        if (i == n-1 && j == n-1) return grid[row][col] + 1;
                        grid[i][j] = grid[row][col] + 1; 
                        q.push({i,j});
                }
            }
        }
        return -1;
    }
};
```       
Runtime: 52 ms, faster than 88.88% of C++ online submissions for Shortest Path in Binary Matrix.         
Memory Usage: 19.3 MB, less than 74.67% of C++ online submissions for Shortest Path in Binary Matrix.         
经讨论区启发的解，时间空间表现都还不错。         

这道题用广搜，元素行走的路径跟之前常见的题就一点不同，从四个方向变成了八个；也好解决，套两个 for 循环，行数列数循环从当前减一到当前加一，加入到队列。依照题目要求，比较人类的想法是，从左上角的 0 开始，将其值置一，开始 while 读写队列；while 中每个和当前队列 front() 元素相邻的元素 0 值元素，值置为当前值加一，然后加入到队列。如果读到位置为右下角的元素，意即到达终点，直接返回当前元素值加一的值。     
如果 while 里面没有触发返回条件，即终点不可达，返回 -1 。      
考虑一种特殊情况，起终点元素非零，直接意为终点不可达，在队列开始前考虑该情况，直接返回 -1 。          


