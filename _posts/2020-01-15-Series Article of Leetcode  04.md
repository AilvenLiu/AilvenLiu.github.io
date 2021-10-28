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

### 130. Surrounded Regions       
Given an m x n matrix board containing 'X' and 'O', capture all regions that are 4-directionally surrounded by 'X'.        
A region is captured by flipping all 'O's into 'X's in that surrounded region.       

Example 1:        
Input: board = [         
["X","X","X","X"],         
["X","O","O","X"],         
["X","X","O","X"],          
["X","O","X","X"]]            
Output:[          
["X","X","X","X"],          
["X","X","X","X"],           
["X","X","X","X"],         
["X","O","X","X"]]             
Explanation: Surrounded regions should not be on the border, which means that any 'O' on the border of the board are not flipped to 'X'. Any 'O' that is not on the border and it is not connected to an 'O' on the border will be flipped to 'X'. Two cells are connected if they are adjacent cells connected horizontally or vertically.         

Example 2:       
Input: board = [["X"]]         
Output: [["X"]]            

Constraints:          
* m == board.length          
* n == board[i].length           
* 1 <= m, n <= 200            
* board[i][j] is 'X' or 'O'.           

#### My AC Version          

```c++       
class Solution {
public:
    int DIRS[5] = {-1,0,1,0,-1};
    void solve(vector<vector<char>>& board) {
        int n = board.size();
        int m = board[0].size();
        if(n<3 || m < 3) return;
        
        queue<pair<int, int>> q;
        for(int i = 0; i < n; i ++){
            if(board[i][0] == 'O') {
                board[i][0] = 'V';
                q.push({i,0});
            }
            if(board[i][m-1] == 'O'){ 
                board[i][m-1] = 'V';
                q.push({i,m-1});
            }
        }
        for(int i = 0; i < m; i ++){
            if(board[0][i] == 'O'){
                board[0][i] = 'V';
                q.push({0,i});
            }
            if(board[n-1][i] == 'O'){ 
                board[n-1][i] = 'V';
                q.push({n-1, i});
            }
        }
        while(!q.empty()){
            pair<int, int> pos = q.front(); q.pop();
            int row = get<0>(pos);
            int col = get<1>(pos);
            for(int i = 0; i < 4; i ++){
                int newRow = row + DIRS[i];
                int newCol = col + DIRS[i+1];
                if (newRow >=0 && newCol >=0 && 
                    newRow < n && newCol < m &&
                    board[newRow][newCol] == 'O'){
                    board[newRow][newCol] = 'V';
                    q.push({newRow, newCol});
                }
            }
        }
        for (int i = 0; i < n; i ++)
            for (int j = 0; j < m; j ++)
                board[i][j] = board[i][j] == 'V' ? 'O' : 'X';
    }
};
```         
Runtime: 18 ms, faster than 32.34% of C++ online submissions for Surrounded Regions.          
Memory Usage: 10 MB, less than 77.46% of C++ online submissions for Surrounded Regions.         

用广搜。这题没什么意思。           
考虑一种特殊情况：行列有一个值小于等于二，这意味着每个元素都是边界元素，直接返回原矩阵即可。否则，执行以下程序：       
先遍历边界元素，将每一个值为 'O' 的元素重新赋值（作为 visited），加入队列。while(!q.empty()) 读写队列，队列内每一个元素的值为零的毗邻元素同样重新赋值，继续加入队列，直到队列为空。遍历矩阵，被重新复制的元素恢复到原值，其他元素置 'X'。        


### 797. All Paths From Source to Target           
Given a directed acyclic graph (DAG) of n nodes labeled from 0 to n - 1, find all possible paths from node 0 to node n - 1 and return them in any order.          
The graph is given as follows: graph[i] is a list of all nodes you can visit from node i (i.e., there is a directed edge from node i to node graph[i][j]).          

Example 1:           
Input: graph = [[1,2],[3],[3],[]]          
Output: [[0,1,3],[0,2,3]]      
Explanation: There are two paths: 0 -> 1 -> 3 and 0 -> 2 -> 3.       

Example 2:      
Input: graph = [[4,3,1],[3,2,4],[3],[4],[]]          
Output: [[0,4],[0,3,4],[0,1,3,4],[0,1,2,3,4],[0,1,4]]          

Example 3:       
Input: graph = [[1],[]]        
Output: [[0,1]]          

Example 4:         
Input: graph = [[1,2,3],[2],[3],[]]        
Output: [[0,1,2,3],[0,2,3],[0,3]]       

Example 5:       
Input: graph = [[1,3],[2],[3],[]]       
Output: [[0,1,2,3],[0,3]]         
 
#### My AC Version          
```c++       
class Solution {
public:
    int n;
    vector<vector<int>> allPathsSourceTarget(vector<vector<int>>& graph) {
        vector<vector<int>> res;
        vector<int> path;
        n = graph.size();
        path.emplace_back(0);
        dfs(graph, res, path, 0);
        return res;
    }
private:
    void dfs(vector<vector<int>>& graph, vector<vector<int>>& res, vector<int>& path, int idx){
        for(int v: graph[idx]){
            path.emplace_back(v);
            if (v == n-1) res.emplace_back(path);
            else dfs(graph, res, path, v);
            path.pop_back();
        }
    }
};
```         
Runtime: 22 ms, faster than 46.76% of C++ online submissions for All Paths From Source to Target.         
Memory Usage: 10.6 MB, less than 85.49% of C++ online submissions for All Paths From Source to Target.       

虽然用了深搜，但这道题也带着回溯的要素。       
先将零元素入容器，开始深搜 dfs 。深搜终点为 元素值等于 n-1 。**如果达不到（else）** n-1 ，将当前元素值作为当前迭代深搜的 idx （由于所有元素值 0 到 n-1，idx 为 i 的 graph 子元素容器存储着 i 节点的出度）继续深搜；如果为 n-1 ，加入 res。递归调用完 dfs，将本次 dfs 加入的元素 pop 出去，为下一轮 dfs 做准备。最后一步是回溯的思想。       


## Day 9 Recursion / Backtracking             
[GitHub 连接](https://github.com/OUCliuxiang/leetcode/blob/master/StudyPlan/Algo2/day09)          

### 78. Subsets         
Given an integer array nums of unique elements, return all possible subsets (the power set).         

The solution set must not contain duplicate subsets. Return the solution in any order.         

Example 1:           
Input: nums = [1,2,3]            
Output: [[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]          

Example 2:       
Input: nums = [0]          
Output: [[],[0]]            
 
Constraints:          
* 1 <= nums.length <= 10            
* -10 <= nums[i] <= 10            
* All the numbers of nums are unique.      

#### My AC Version           
```c++           
class Solution {
public:
    int n;
    vector<vector<int>> subsets(vector<int>& nums) {
        vector<vector<int>> res;
        n = nums.size();
        vector<int> cur;
        res.emplace_back(cur);
        for (int i = 0; i < n; i++){
            cur.emplace_back(nums[i]);
            res.emplace_back(cur);
            helper(nums, res, cur, i+1);
            cur.pop_back();
        }
        return res;
    }
private:
    void helper(vector<int>& nums, vector<vector<int>>& res, 
                vector<int>& cur, int idx){
        if (idx == n) return;
        for (int i = idx; i < n; i ++){
            cur.emplace_back(nums[i]);
            res.emplace_back(cur);
            helper(nums, res, cur, i+1);
            cur.pop_back();
        }
    }
};
```            
Runtime: 0 ms, faster than **100.00%** of C++ online submissions for Subsets.         
Memory Usage: 7.1 MB, less than 83.87% of C++ online submissions for Subsets.         

递归+回溯。        
先加入空数组到 res ，开始遍历。由于数组内元素无重复，直接从 0 遍历到数组结束，加入当前元素到 cur ，随后加入当前 cur 到 res 。在此（cur 已存在的元素）基础上，从 idx+1 个元素递归调用 helper，以在此基础上继续加入元素且避免重复。完成递归调用后随即将当前加入的元素 pop 出来，给下一个元素留出位置。          

### 90. Subsets II               
Given an integer array nums that may contain duplicates, return all possible subsets (the power set).             
The solution set must not contain duplicate subsets. Return the solution in any order.           

Example 1:          
Input: nums = [1,2,2]            
Output: [[],[1],[1,2],[1,2,2],[2],[2,2]]            

Example 2:         
Input: nums = [0]            
Output: [[],[0]]           

Constraints:             
* 1 <= nums.length <= 10            
* -10 <= nums[i] <= 10            

#### My AC Version            
```c++            
class Solution {
public:
    int n;
    vector<vector<int>> subsetsWithDup(vector<int>& nums) {
        vector<vector<int>> res;
        n = nums.size();
        sort(nums.begin(), nums.end());
        vector<int> cur;
        res.emplace_back(cur);
        for (int i = 0; i < n; i++){
            if (i>0 && find(nums.begin(), nums.begin()+i, nums[i]) != nums.begin()+i) continue;
            cur.emplace_back(nums[i]);
            res.emplace_back(cur);
            helper(nums, res, cur, i+1);
            cur.pop_back();
        }
        return res;
    }
private:
    void helper(vector<int>& nums, vector<vector<int>>& res, vector<int>& cur, int idx){
        if (idx == n) return;
        for (int i = idx; i < n; i ++){
            if (i>idx && find(nums.begin()+idx, nums.begin()+i, nums[i]) != nums.begin()+i) continue;
            cur.emplace_back(nums[i]);
            res.emplace_back(cur);
            helper(nums, res, cur, i+1);
            cur.pop_back();
        }
    }
};
```         
Runtime: 0 ms, faster than **100.00%** of C++ online submissions for Subsets II.           
Memory Usage: 7.5 MB, less than 88.83% of C++ online submissions for Subsets II.           

和上一题没什么区别，唯一不同点是本题存在重复元素，需要避开：在每次执行加入元素到 cur、加入 cur 到 res、pop 出当前添加的一个元素等等一系列操作之前，先做一次判断，判断从当前步骤（添加长度为 x 的数组到 res）的起始元素到当前元素的前一个元素之间有没有元素和当前元素相同，如果有的话，即意味着当前元素在当前一步骤中已经添加过了（for 循环内的第一步操作），可以直接 continue 。       

## Day 10 Recursion / Backtracking             
[GitHub 连接](https://github.com/OUCliuxiang/leetcode/blob/master/StudyPlan/Algo2/day10)          

## 47. Permutations II            
Given a collection of numbers, nums, that might contain duplicates, return all possible unique permutations in any order.           

Example 1:          
Input: nums = [1,1,2]          
Output:            
[[1,1,2],            
 [1,2,1],          
 [2,1,1]]           

Example 2:          
Input: nums = [1,2,3]              
Output: [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]           

Constraints:          
* 1 <= nums.length <= 8           
* -10 <= nums[i] <= 10           

对于排列问题，c++11 提供了现成的轮子 `next_permutations(pos1, pos2)`；基于之，我们可以用寥寥几行代码得出结果：        

#### next_premutation Solution          
```c++        
class Solution {
public:
    vector<vector<int>> permuteUnique(vector<int>& nums) {
        std::sort(nums.begin(), nums.end());
        vector<vector<int>> res;
        do{
            res.emplace_back(nums);
        }while(next_permutation(nums.begin(), nums.end()));
        return res;
    }
};
```         
其时间空间表现也不差，甚至当数组越复杂，其表现越好。        


上述方法毕竟取巧，无法理解排列问题的本质思想。      
实际上，这个题是 46题 Permutations 的进阶版本，和 46 的唯一区别就是数组里存在重复元素，我们需要避开。            
先看一下 46 题无重复元素情况的时间击败 **100%** 的解。         

#### 46 Solution          
```c++          
class Solution {
public:
    int len;
    vector<vector<int>> permute(vector<int>& nums) {
        len = nums.size();
        sort( nums.begin(), nums.end());
        vector<vector<int>> res;
        recursion(res, nums, 0);
        return res;
    }
private:
    void recursion( vector<vector<int>>& res, 
                    vector<int>& nums, int idx){
        if (idx == len){
            res.emplace_back(nums);
            return;
        }
        for (int i = idx; i < len; i ++){
            swap(nums[i], nums[idx]);
            recursion(res, nums, idx+1);
            swap(nums[i], nums[idx]);
        }
    }
};
```              

Runtime: 0 ms, faster than 100.00% of C++ online submissions for Permutations.         
Memory Usage: 8.1 MB, less than 29.05% of C++ online submissions for Permutations.           

比较容易理解的一个思路，对从 idx=0 位置开始的每个位置，通过递归调用依次交换该位置和其后面每一个位置的元素的值，就能得到所有不同的排列。对每一个位置，完成一次交换并调用递归后，需要取消交换（再对同一对数 swap 一次） 恢复原来状态，给当前位置的下一次交换做准备。           

#### My AC Version            
```c++
class Solution {
public:
    int len;
    vector<vector<int>> permuteUnique(vector<int>& nums) {
        len = nums.size();
        sort( nums.begin(), nums.end());
        vector<vector<int>> res;
        recursion(res, nums, 0);
        return res;
    }
private:
    void recursion( vector<vector<int>>& res, 
                    vector<int> nums, int idx){
        if (idx == len){
            res.emplace_back(nums);
            return;
        }
        for (int i = idx; i < len; i ++){
            if (i != idx && nums[i] == nums[idx]) continue;
            swap(nums[i], nums[idx]);
            recursion(res, nums, idx+1);
        }
    }
};
```          
Runtime: 14 ms, faster than 42.30% of C++ online submissions for Permutations II.          
Memory Usage: 9.2 MB, less than 55.02% of C++ online submissions for Permutations II.            

在 46 基础上改进的一个解。除了判断相同元素 continue 外，只有两点不同：       
1. 数组传入是没有使用引用；           
2. 递归调用后没有再进行进行 swap 操作。       

原因是在于重复元素、尤其是多个重复元素的存在会导致基于不同层的**交换**出现相同的结果，比如对测试样例 [1,1,2,2]， 在当前排列基础上 swap( idx1, idx3) 结果为 [1,2,2,1]；当排列的 idx 1,2 已完成交换，排列变为 [1,2,1,2]，在此基础上 swap( idx3, idx4) 结果也为 [1,2,2,1]。按照无重复元素设计的“依次交换”方案无法避免这种情况，于是，采用一种新的思路：从 idx 开始，将 idx 后每一个和**当前**（当前可能已被交换）元素不相等的元素**往前挪**，也即连续 swap 而不取消。效果为：         
idx = 0:  [1,2,3,4] --> [2,1,3,4] --> [3,1,2,4] --> [4,1,2,3]          
于是不使用引用的原因就显而易见了：避免递归调用对当前元素顺序产生影响。           

### 39. Combination Sum            
Given an array of distinct integers candidates and a target integer target, return a list of all unique combinations of candidates where the chosen numbers sum to target. You may return the combinations in any order.           
The same number may be chosen from candidates an unlimited number of times. Two combinations are unique if the frequency of at least one of the chosen numbers is different.            
It is guaranteed that the number of unique combinations that sum up to target is less than 150 combinations for the given input.           

Example 1:           
Input: candidates = [2,3,6,7], target = 7            
Output: [[2,2,3],[7]]             
Explanation:             
2 and 3 are candidates, and 2 + 2 + 3 = 7. Note that 2 can be used multiple times.            
7 is a candidate, and 7 = 7.           
These are the only two combinations.           

Example 2:          
Input: candidates = [2,3,5], target = 8            
Output: [[2,2,2,2],[2,3,3],[3,5]]             

Example 3:           
Input: candidates = [2], target = 1            
Output: []           

Example 4:         
Input: candidates = [1], target = 1          
Output: [[1]]             

Example 5:           
Input: candidates = [1], target = 2           
Output: [[1,1]]            

#### My AC Version          
```c++           
class Solution {
public:
    int len;
    std::vector<std::vector<int>> combinationSum( 
    std::vector<int>& nums, int target) {
        std::sort(nums.begin(), nums.end());
        len = nums.size();
        std::vector<int> cur;
        std::vector<std::vector<int>> res;
        finder(res, cur, nums, target, 0);
        return res;
    }
private:
    void finder(std::vector<std::vector<int>>& res, 
                std::vector<int>& cur, 
                std::vector<int>& nums, 
                int target, 
                int idx){
        if (target == 0){
            res.emplace_back(cur);
            return;
        }
        for (int i = idx; i < len && target >= nums[i]; i++){
            cur.emplace_back(nums[i]);
            finder(res, cur, nums, target-nums[i], i);
            cur.pop_back();
        }
    }
};
```
Runtime: 9 ms, faster than 55.52% of C++ online submissions for Combination Sum.          
Memory Usage: 10.7 MB, less than 97.09% of C++ online submissions for Combination Sum.         
递归回溯去做没什么难理解的。由于元素可以复用，每一次递归调用的 idx 都从当前开始。 注意 for 循环的终止条件有一个 target \< nums[i]，由于 nums 是有序的，如果当前 nums[i] 大于 target，也就没有继续进行下去的必要了。         

### 40. Combination Sum II        
Given a collection of candidate numbers (candidates) and a target number (target), find all unique combinations in candidates where the candidate numbers sum to target.          
Each number in candidates may only be used once in the combination.         
Note: The solution set must not contain duplicate combinations.         
 
Example 1:        
Input: candidates = [10,1,2,7,6,1,5], target = 8            
Output:       
[[1,1,6],       
 [1,2,5],           
 [1,7],        
 [2,6]]       

Example 2:         
Input: candidates = [2,5,2,1,2], target = 5            
Output:           
[[1,2,2],         
 [5]]          

#### My AC Version         
```c++           
class Solution {
public:
    int len;
    std::vector<std::vector<int>> combinationSum2(
    std::vector<int>& nums, int target) {
        len = nums.size();
        std::sort(nums.begin(), nums.end());
        std::vector<std::vector<int>> res;
        std::vector<int> cur;
        finder(res, cur, nums, target, 0);
        return res;
    }
private:
    void finder(std::vector<std::vector<int>>& res,
                std::vector<int>& cur,
                std::vector<int>& nums,
                int target, 
                int idx){
        if(!target){
            res.emplace_back(cur);
            return;
        }
        for(int i = idx; i < len && target >= nums[i]; i++){
            if (i != idx && nums[i] == nums[idx]) continue;
            cur.emplace_back(nums[i]);
            finder(res, cur, nums, target-nums[i], i+1);
            cur.pop_back();
        }
    }
};
```         
Runtime: 3 ms, faster than 92.41% of C++ online submissions for Combination Sum II.         
Memory Usage: 10.7 MB, less than 50.62% of C++ online submissions for Combination Sum II.         
跟上一题一样，也没什么特别难想到的地方。由于元素不能复用，每一次递归调用的 idx 应当从当前 +1 开始。由于存在重复元素，在递归调用前需要判断当前元素值是否与 idx 相同，是则跳过。        
