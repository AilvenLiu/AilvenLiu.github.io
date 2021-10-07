---
layout:     post
title:      Series Article of Leetcode Notes -- 08
subtitle:   Daily Problem      
date:       2021-10-07
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - leetcode      
    - c++     
    - algorithm            
---     

> leetcode 刷题笔记，Daily Problem 。     

### 79. Word Search         
Given an m x n grid of characters board and a string word, return true if word exists in the grid.           
The word can be constructed from letters of sequentially adjacent cells, where adjacent cells are horizontally or vertically neighboring. The same letter cell may not be used more than once.      
 
Example 1:         
Input: board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCCED"          
Output: true

Example 2:           
Input: board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "SEE"        
Output: true           

Example 3:        
Input: board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCB"          
Output: false          

Constraints:           
* m == board.length           
* n = board[i].length          
* 1 <= m, n <= 6           
* 1 <= word.length <= 15            
* board and word consists of only lowercase and uppercase English letters.

#### My WA Version       
```c++          
class Solution {
public:
    int DIRS[5] = {-1, 0, 1, 0, -1};
    int Row, Col;
    bool exist(vector<vector<char>>& board, string word) {
        Row = board.size();
        Col = board[0].size();
        bool flag = false;
        for (int i = 0; i < Row; i ++)
            for (int j = 0; j < Col; j ++)
                if (board[i][j] == word[0]){
                    vector<vector<bool>> mask(Row, vector<bool>(Col, false));
                    dfs(board, word, flag, mask, i, j, 0);
                    if (flag == true)
                        return true;
                }
        return false;
    }
private:
    void dfs(vector<vector<char>>& board, string& word, bool& flag, 
             vector<vector<bool>>& mask, int i, int j, int k){
        if (i < 0 || i >= Row || j < 0 || j >= Col) return;
        printf("%d(%c): [%d, %d]\n", k, word[k], i, j);
        if (mask[i][j]) return;
        if (board[i][j] != word[k]) return;
        mask[i][j] = true;
        if (k == word.size()-1) {
            flag = true;
            return;
        }
        for (int l = 0; l < 4; l++)
            dfs(board, word, flag, mask, i+DIRS[l], j+DIRS[l+1], k+1);
    }
};
```         
Wrong Answer, 55 / 57 test cases passed.         

Failed Example:         
Input: [["A","B","C","E"],["S","F","E","S"],["A","D","E","E"]]
"ABCEFSADEESE"        
Output: false         
Expected: true       

比较循规蹈矩的一个使用 mask 矩阵记录走过位置的深搜。 WA 的问题出在当一条路走不通要换另一条路的时候，此前走错的路被标记为走过而无法再次进入。暂想不到解决办法，看讨论区。         

#### Discuss solution        

经讨论区启发，解决上述解法存在问题的方法极其简单：在递归调用 dfs 语句后再加一行 `mask[i][j] = false;` 将走过的路在当前递归结束后恢复原值就好了嘛！       
```c++          
class Solution {
public:
    int DIRS[5] = {-1, 0, 1, 0, -1};
    int Row, Col;
    bool exist(vector<vector<char>>& board, string word) {
        Row = board.size();
        Col = board[0].size();
        bool flag = false;
        for (int i = 0; i < Row; i ++)
            for (int j = 0; j < Col; j ++)
                if (board[i][j] == word[0]){
                    vector<vector<bool>> mask(Row, vector<bool>(Col, false));
                    dfs(board, word, flag, mask, i, j, 0);
                    if (flag == true)
                        return true;
                }
        return false;
    }
private:
    void dfs(vector<vector<char>>& board, string& word, bool& flag, 
             vector<vector<bool>>& mask, int i, int j, int k){
        if (i < 0 || i >= Row || j < 0 || j >= Col) return;
        if (mask[i][j]) return;
        if (board[i][j] != word[k]) return;
        mask[i][j] = true;
        if (k == word.size()-1) {
            flag = true;
            return;
        }
        for (int l = 0; l < 4; l++)
            dfs(board, word, flag, mask, i+DIRS[l], j+DIRS[l+1], k+1);
        mask[i][j] = false;
    }
};
```         
Runtime: 216 ms, faster than 82.47% of C++ online submissions for Word Search.         
Memory Usage: 7.6 MB, less than 19.78% of C++ online submissions for Word Search.          

时间表现过得去，空间表现差了点，是因为我们另外开辟了一个矩阵空间作为 visited 矩阵。实际上，按照讨论区，这个 visited 矩阵 duck 不必，完全可以通过改变当前访问的矩阵元素值等效 visited 矩阵：           
```c++         
class Solution {
public:
    int DIRS[5] = {-1, 0, 1, 0, -1};
    int Row, Col;
    bool exist(vector<vector<char>>& board, string word) {
        Row = board.size();
        Col = board[0].size();
        bool flag = false;
        for (int i = 0; i < Row; i ++)
            for (int j = 0; j < Col; j ++)
                if (board[i][j] == word[0]){
                    dfs(board, word, flag, i, j, 0);
                    if (flag == true)
                        return true;
                }
        return false;
    }
private:
    void dfs(vector<vector<char>>& board, string& word, 
             bool& flag, int i, int j, int k){
        if (i < 0 || i >= Row || j < 0 || j >= Col) return;
        if (board[i][j] != word[k]) return;
        if (k == word.size()-1) {
            flag = true;
            return;
        }
        char t = board[i][j];
        board[i][j] = '*';
        for (int l = 0; l < 4; l++)
            dfs(board, word, flag, i+DIRS[l], j+DIRS[l+1], k+1);
        board[i][j] = t;
    }
};
```      
Runtime: 196 ms, faster than 89.08% of C++ online submissions for Word Search.         
Memory Usage: 7.3 MB, less than 82.16% of C++ online submissions for Word Search.           
不错，空间表现都有很大进步。           
