---
layout:     post
title:      Series Article of Leetcode Notes -- 06
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
> 从 DS 开始转战力扣中文站，虽然界面难看，加载太慢，但讨论区似乎比全球站更贴近人类的思想：稳扎稳打从基础知识演化分析，没有那么多奇技淫巧。        

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

#### Thought           
这题，明显用动态规划去做。先考虑对数组长度为 1 的特殊情况，直接返回 nums.back()。       
构造长度为 len = nums.size() 的 dp 数组，dp[i] 代表直到 nums 以 idx = i 为尾部的当前最大连续子序列和；同时维护一个 res 变量存储全局最大连续子序列和。 dp 数组的更新策略（状态转移）则为：     
1. dp[i-1] < 0，此时当前最大子序列和不能定义为前一个最大自序和与当前值加和，因为显然和当前值相比后者更大；应直接 dp[i] = nums[i]。       
2. 否则，显然为 dp[i] = dp[i-1] + nums[i]。     
 
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

#### Thought           
经典题目了，这题用暴力罚不会超时，但不应该使用暴力：使用哈希结构的 `unordered_set` 进行搜索可以将时间复杂度降到 O(n)。     

把所有的元素加入到 unordered_set 中，开始遍历 nums，分两种情况讨论：    
1. target - nums[i] == nums[i]。此时从 i+1 寻找值为 nums[i] 的元素，如果存在，直接返回；      
2. 否则，先从 unordered_set 中 erase 删掉值为 nums[i] 的元素，然后查找值为 target - nums[i] 的元素。如果找到，直接返回。       

如果遍历完全部元素而仍没有返回，则不存在这样两个元素，返回 {0,0}。        

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

### 88. 合并两个有序数组      
给你两个按 非递减顺序 排列的整数数组 nums1 和 nums2，另有两个整数 m 和 n ，分别表示 nums1 和 nums2 中的元素数目。      
请你 合并 nums2 到 nums1 中，使合并后的数组同样按 非递减顺序 排列。        
注意：最终，合并后数组不应由函数返回，而是存储在数组 nums1 中。为了应对这种情况，nums1 的初始长度为 m + n，其中前 m 个元素表示应合并的元素，后 n 个元素为 0 ，应忽略。nums2 的长度为 n 。        

示例 1：      
输入：nums1 = [1,2,3,0,0,0], m = 3, nums2 = [2,5,6], n = 3        
输出：[1,2,2,3,5,6]      
解释：需要合并 [1,2,3] 和 [2,5,6] 。     
合并结果是 [1,2,2,3,5,6] ，其中斜体加粗标注的为 nums1 中的元素。       

示例 2：      
输入：nums1 = [1], m = 1, nums2 = [], n = 0       
输出：[1]            
解释：需要合并 [1] 和 [] 。              
合并结果是 [1] 。              

示例 3：     
输入：nums1 = [0], m = 0, nums2 = [1], n = 1     
输出：[1]        
解释：需要合并的数组是 [] 和 [1] 。           
合并结果是 [1] 。       
注意，因为 m = 0 ，所以 nums1 中没有元素。nums1 中仅存的 0 仅仅是为了确保合并结果可以顺利存放到 nums1 中。       

提示：       
* nums1.length == m + n       
* nums2.length == n      
* 0 <= m, n <= 200        
* 1 <= m + n <= 200        
* -109 <= nums1[i], nums2[j] <= 109      

#### Thought    
这题目有意思，应该归类到双指针：同时维护属于（用于） num1, nums2 的两个 idx： i, j，在一个循环里面保持同步更新：       
1. 均从零开始；且由于最后 nums1 的长度为 m+n ，则 i~[0, m+n)。       
2. 考虑 nums2[j] < nums1[i]，在 nums1.begin()+i 位置（之前）插入 nums2[j]，i, j 均向后走一位。      
3. 否则，i 向后走一位。     
4. 在前两步骤之前，也即每次循环最开始，考虑一种特殊情况：i 已经走到了值无意义处，也就是 i>=m+j，nums1[i] 处已经没有可比较的值，此时直接在nums1.begin()+i 出（之前）插入 nums2[j]；同时 i, j 均向后走一位。          

#### My AC Version       

```c++       
class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        int i = 0, j = 0;
        for (;i<m+n && j<n;){
            if (i>=m+j) nums1.emplace(nums1.begin()+i++, nums2[j++]);
            else if (nums2[j]<nums1[i]) 
                nums1.emplace(nums1.begin()+i++, nums2[j++]);
            else i++;
        }
        nums1.resize(m+n);
    }
};
```        

## 第 3 天 数组         

### 350. 两个数组的交集 II      
给定两个数组，编写一个函数来计算它们的交集。         

示例 1：      
输入：nums1 = [1,2,2,1], nums2 = [2,2]   
输出：[2,2]             

示例 2:     
输入：nums1 = [4,9,5], nums2 = [9,4,9,8,4]    
输出：[4,9]          

说明：       
* 输出结果中每个元素出现的次数，应与元素在两个数组中出现次数的最小值一致。      
* 我们可以不考虑输出结果的顺序。      

#### Thought     
这题如果是有序数组就好了，所以先排个序。      
排完序后，发现这玩意儿本质上又是个双指针问题：同时维护两个 i, j 两个 idx：     
1. nums1[i] == nums2[j]，该值加入 res 数组； i++, j++ 。       
2. nums1[i] < nums2[j]，i++。        
3. nums1[i] > nums2[j]，j++。       

#### My AC Version      
```c++       
class Solution {
public:
    std::vector<int> intersect(std::vector<int>& nums1, std::vector<int>& nums2) {
        std::sort(nums1.begin(), nums1.end());
        std::sort(nums2.begin(), nums2.end());
        int i = 0, j = 0;
        std::vector<int> res;
        while(i<nums1.size() && j<nums2.size()){
            if (nums1.at(i) == nums2.at(j)){ 
                res.emplace_back(nums1.at(i));
                i++;j++;
            }
            else if (nums1.at(i) < nums2.at(j)) i++;
            else if (nums1.at(i) > nums2.at(j)) j++;
        }
        return res;
    }
};
```     
执行用时：0 ms, 在所有 C++ 提交中击败了100.00% 的用户     
内存消耗：9.7 MB, 在所有 C++ 提交中击败了88.15% 的用户     

### 121. 买卖股票的最佳时机         
给定一个数组 prices ，它的第 i 个元素 prices[i] 表示一支给定股票第 i 天的价格。        
你只能选择 某一天 买入这只股票，并选择在 未来的某一个不同的日子 卖出该股票。设计一个算法来计算你所能获取的最大利润。          
返回你可以从这笔交易中获取的最大利润。如果你不能获取任何利润，返回 0 。     

示例 1：      
输入：[7,1,5,3,6,4]       
输出：5          
解释：在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。       
注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格；同时，你不能在买入前卖出股票。         

示例 2：       
输入：prices = [7,6,4,3,1]        
输出：0          
解释：在这种情况下, 没有交易完成, 所以最大利润为 0。          

提示：        
* 1 <= prices.length <= 105         
* 0 <= prices[i] <= 104

#### Thought     
这道题，有意思；可以用动态解，也可以遍历解。      
相对而言，遍历解的思想比较贴近人类：遍历数组，并使用预先构造好的变量 minVal 维护一个最小值；遍历过程中使用预先构造好的 result 变量维护当前值与 minVal 的差的最大值。最后返回 result 即使结果。            

#### My AC Version       
```c++      
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int len = prices.size();
        int in = 0xffffff, result = 0;
        for (int i = 0; i < len-1; i++){
            in = min(in, prices[i]);
            result = max(result, prices[i+1] - in);
        }
        return result;
    }
};
```     
执行用时：84 ms, 在所有 C++ 提交中击败了98.32% 的用户     
内存消耗：91.1 MB, 在所有 C++ 提交中击败了78.15% 的用户         

#### 动态规划解       
参见[力扣股票买卖系列问题专题博客]()。 

## 第 4 天 数组         

### 566. 重塑矩阵       
在 MATLAB 中，有一个非常有用的函数 reshape ，它可以将一个 m x n 矩阵重塑为另一个大小不同（r x c）的新矩阵，但保留其原始数据。        
给你一个由二维数组 mat 表示的 m x n 矩阵，以及两个正整数 r 和 c ，分别表示想要的重构的矩阵的行数和列数。        
重构后的矩阵需要将原始矩阵的所有元素以相同的 行遍历顺序 填充。       
如果具有给定参数的 reshape 操作是可行且合理的，则输出新的重塑矩阵；否则，输出原始矩阵。        

示例 1：       
输入：mat = [[1,2],[3,4]], r = 1, c = 4         
输出：[[1,2,3,4]]      

示例 2：       
输入：mat = [[1,2],[3,4]], r = 2, c = 4      
输出：[[1,2],[3,4]]     

提示：      
* m == mat.length        
* n == mat[i].length         
* 1 <= m, n <= 100         
* -1000 <= mat[i][j] <= 1000          
* 1 <= r, c <= 300        

#### Thought     
这题，没什么说的。构造一个一维 vector tmp 存储行数据， 遍历原始数组并向 tmp emplace_back 元素；每当 tmp.size() 达到 c，将当前 tmp emplace_back 进入 res ，并清空。没了。           

#### My AC Version      
```c++    
class Solution {
public:
    std::vector<std::vector<int>> matrixReshap ( 
    std::vector<std::vector<int>>& mat, int r, int c) {
        if(mat.size() * mat[0].size() != r*c) return mat;
        std::vector<std::vector<int>> res;
        std::vector<int> tmp;
        for (int i = 0; i < mat.size(); i++){
            for (int j = 0; j < mat[i].size(); j++){
                tmp.emplace_back(mat[i][j]);
                if(tmp.size() == c){
                    res.emplace_back(tmp);
                    tmp.clear();
                }
            }
        }
        return res;

    }
};
```      

### 118. 杨辉三角        
给定一个非负整数 numRows，生成「杨辉三角」的前 numRows 行。       
在「杨辉三角」中，每个数是它左上方和右上方的数的和。        

示例 1:     
输入: numRows = 5         
输出: [[1],[1,1],[1,2,1],[1,3,3,1],[1,4,6,4,1]]         
 
示例 2:           
输入: numRows = 1         
输出: [[1]]        

提示:        
* 1 <= numRows <= 30         

#### Thought       
这题也没什么说的，从第二行（idx = 1） 开始，把每一行的首位元素和中间元素分开讨论即可。           

#### My AC Version        
```c++       
class Solution {
public:
    std::vector<std::vector<int>> generate(int numRows) {
        std::vector<std::vector<int>> res;
        std::vector<int> tmp = {1};
        res.emplace_back(tmp);
        tmp.clear();
        for (int i = 1; i < numRows; i++){
            std::vector<int> tmp;
            for (int j = 0; j <= i; j ++){
                int val;
                if(j == 0) val = res.at(i-1).front();
                else if ( j == i) val = res.at(i-1).back();
                else val = res.at(i-1).at(j-1) + res.at(i-1).at(j);
                tmp.emplace_back(val);
            }
            res.emplace_back(tmp);
        }
        return res;
    }
};
```       

## 第 5 天 数组         

### 36. 有效的数独     
请你判断一个 9x9 的数独是否有效。只需要 根据以下规则 ，验证已经填入的数字是否有效即可。   
1. 数字 1-9 在每一行只能出现一次。      
2. 数字 1-9 在每一列只能出现一次。     
3. 数字 1-9 在每一个以粗实线分隔的 3x3 宫内只能出现一次。（请参考示例图）      

数独部分空格内已填入了数字，空白格用 '.' 表示。       
注意：      
* 一个有效的数独（部分已被填充）不一定是可解的。       
* 只需要根据以上规则，验证已经填入的数字是否有效即可。       

示例 1：       
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/leetcode/leetcode036.png"></div>       
   
输入：board =       
[["5","3",".",".","7",".",".",".","."]    
,["6",".",".","1","9","5",".",".","."]         
,[".","9","8",".",".",".",".","6","."]               
,["8",".",".",".","6",".",".",".","3"]            
,["4",".",".","8",".","3",".",".","1"]           
,["7",".",".",".","2",".",".",".","6"]           
,[".","6",".",".",".",".","2","8","."]          
,[".",".",".","4","1","9",".",".","5"]          
,[".",".",".",".","8",".",".","7","9"]]        
输出：true         

示例 2：     
输入：board =    
[["8","3",".",".","7",".",".",".","."]     
,["6",".",".","1","9","5",".",".","."]      
,[".","9","8",".",".",".",".","6","."]         
,["8",".",".",".","6",".",".",".","3"]       
,["4",".",".","8",".","3",".",".","1"]      
,["7",".",".",".","2",".",".",".","6"]         
,[".","6",".",".",".",".","2","8","."]        
,[".",".",".","4","1","9",".",".","5"]        
,[".",".",".",".","8",".",".","7","9"]]        
输出：false        
解释：除了第一行的第一个数字从 5 改为 8 以外，空格内其他数字均与 示例1 相同。 但由于位于左上角的 3x3 宫内有两个 8 存在, 因此这个数独是无效的。        

提示：      
* board.length == 9      
* board[i].length == 9         
* board[i][j] 是一位数字或者 '.'          

#### Thought      
这题具体实现的代码有些啰嗦，但其实现思路却相当明晰：使用一个固定长度为 9 的记录数组存储每行、列、块中数字出现的次数（每完成一次行、列、块的扫描就置零）。从行、列、块三个维度三次遍历数组，如果记录数组出现某值为零的情况，返回 false ，也即该数独不合法。默认返回值 true 代表完整完成三次数组遍历没有返回，即数独合法。       

#### My AC Version       
```c++     
class Solution {
public:
    bool isValidSudoku(vector<vector<char>>& board) {
        int record[9]; memset(record, 0, sizeof(int)*9);
        
        for (int i = 0; i < 9; i ++){
            for (int j = 0; j < 9; j++){
                if (board[i][j] == '.') continue;
                if(record[board[i][j] - '1'] == 1) return false;
                record[board[i][j]-'1']++;
            }
            memset(record, 0, sizeof(int)*9);
        }

        for (int j = 0; j < 9; j ++){
            for (int i = 0; i < 9; i++){
                if (board[i][j] == '.') continue;
                if(record[board[i][j] - '1'] == 1) return false;
                record[board[i][j]-'1']++;
            }
            memset(record, 0, sizeof(int)*9);
        }

        for (int i = 0; i < 3; i++){
            for (int j = 0; j < 3; j++){
                for (int r = 0; r < 3; r++){
                    for (int c = 0; c < 3; c++){
                        if (board[i*3+r][j*3+c] == '.') continue;
                        if (record[board[i*3+r][j*3+c] - '1'] == 1) return false;
                        record[board[i*3+r][j*3+c] - '1'] ++;
                    }
                }
                memset(record, 0, sizeof(int)*9);
            }
        }

        return true;
    }
};
```        
执行用时：20 ms, 在所有 C++ 提交中击败了60.31% 的用户        
内存消耗：17.3 MB, 在所有 C++ 提交中击败了99.67% 的用户         

### 73. 矩阵置零           
给定一个 m x n 的矩阵，如果一个元素为 0 ，则将其所在行和列的所有元素都设为 0 。请使用 原地 算法。         
进阶：       
* 一个直观的解决方案是使用  O(mn) 的额外空间，但这并不是一个好的解决方案。       
* 一个简单的改进方案是使用 O(m + n) 的额外空间，但这仍然不是最好的解决方案。        
* 你能想出一个仅使用常量空间的解决方案吗？     

示例 1：        
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/leetcode/leetcode073-1.jpg"></div>       
   
输入：matrix = [[1,1,1],[1,0,1],[1,1,1]]       
输出：[[1,0,1],[0,0,0],[1,0,1]]           

示例 2：
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/leetcode/leetcode073-2.jpg"></div>       
   
输入：matrix = [[0,1,2,0],[3,4,5,2],[1,3,1,5]]         
输出：[[0,0,0,0],[0,4,5,0],[0,3,1,0]]        

提示：        
* m == matrix.length       
* n == matrix[0].length        
* 1 <= m, n <= 200        
* -231 <= matrix[i][j] <= 231 - 1           
 
#### Thought           
这题也是，实现稍显啰嗦，思路却很明晰。     
建立两个固定长度数组用来记录需要被置零的行坐标和列坐标，然在矩阵中寻找零值——值得注意的是，这个过程不需要双层 for 循环，更高效的做法是一层 for 循环做行遍历，再对每一行使用 find + while 找出所有符合条件的值（零值）。对行列两个数组完成赋值后，即可对矩阵相应行列进行置零操作。         

#### My AC Version       
```c++      
class Solution {
public:
    void setZeroes(vector<vector<int>>& matrix) {
        bool rowRecord[matrix.size()]; 
        memset(rowRecord, false, sizeof(bool) * matrix.size());
        bool colRecord[matrix[0].size()];
        memset(colRecord, false, sizeof(bool) * matrix[0].size());
        
        for (int i = 0; i < matrix.size(); i++){
            int zeroCol = find( matrix[i].begin(), matrix[i].end(), 0) - matrix[i].begin();
            while (zeroCol != matrix[i].size()){
                rowRecord[i] = true;
                colRecord[zeroCol] = true;
                zeroCol = find( matrix[i].begin()+zeroCol+1, matrix[i].end(), 0) - matrix[i].begin();
            }
        }
        for (int i = 0; i < matrix.size(); i++)
            if (rowRecord[i]) fill( matrix[i].begin(), matrix[i].end(), 0);
        for (int j = 0; j < matrix[0].size(); j++){
            if(colRecord[j])
                for(int i = 0; i < matrix.size(); i++)
                    matrix[i][j] = 0;
        }
        
    }
};
```        

## 第 6 天 字符串       
### 387. 字符串中的第一个唯一字符        
给定一个字符串，找到它的第一个不重复的字符，并返回它的索引。如果不存在，则返回 -1。     
示例：       
s = "leetcode"         
返回 0         

s = "loveleetcode"      
返回 2      

提示：你可以假定该字符串只包含小写字母。       

#### Thought     
建立一个 unordered_map 记录字符串中每个字符出现的次数，一目了然。         

#### My AC Version        
```c++       
class Solution {
public:
    int firstUniqChar(string s) {
        unordered_map<char, int> record;
        for (char ch: s)
            record[ch] ++;
        
        for (int i = 0; i < s.size(); i++){
            if (record[s[i]] == 1)
            return i;
        }
        return -1;
    }
};
```      

### 383. 赎金信          
给定一个赎金信 (ransom) 字符串和一个杂志(magazine)字符串，判断第一个字符串 ransom 能不能由第二个字符串 magazines 里面的字符构成。如果可以构成，返回 true ；否则返回 false。      
(题目说明：为了不暴露赎金信字迹，要从杂志上搜索各个需要的字母，组成单词来表达意思。杂志字符串中的每个字符只能在赎金信字符串中使用一次。)         

示例 1：       
输入：ransomNote = "a", magazine = "b"        
输出：false          

示例 2：         
输入：ransomNote = "aa", magazine = "ab"            
输出：false         

示例 3：         
输入：ransomNote = "aa", magazine = "aab"      
输出：true         

提示：          
* 你可以假设两个字符串均只含有小写字母。       

#### Thought          
显然也是要建立一个 unordered_map 记录字母出现的次数的。需要考虑的只是 map 记录赎金信还是杂志。想象一下，写赎金信的时候，要从杂志上一个一个剪去相应字母，所以 map 记录的应该是杂志中字母出现的次数。而写赎金信的过程既是遍历赎金信字符串，将 map 中对应字符个数减一。      

#### My AC Version
```c++       
class Solution {
public:
    bool canConstruct(std::string ransomNote, std::string magazine) {
        std::unordered_map<char, int> record;
        for (char ch: magazine) record[ch]++;
        for (char ch: ransomNote){
            record[ch] --;
            if (record[ch]<0) return false;
        }
        return true;
    }
};
```     

### 242. 有效的字母异位词      
给定两个字符串 s 和 t ，编写一个函数来判断 t 是否是 s 的字母异位词。        
注意：若 s 和 t 中每个字符出现的次数都相同，则称 s 和 t 互为字母异位词。     

示例 1:         
输入: s = "anagram", t = "nagaram"         
输出: true        

示例 2:         
输入: s = "rat", t = "car"         
输出: false           

提示:         
* 1 <= s.length, t.length <= 5 * 104        
* s 和 t 仅包含小写字母        

#### Thought        
建立一个长度为 26 的记录数组存储字符串中每个字母出现的次数。遍历一个字符串，为记录数组赋值，再遍历另一个字符串减去记录数组中相应字母的个数。最后遍历一遍记录数组，一旦出现非零元素，返回 false 。      

#### My AC Version        
```c++       
class Solution {
public:
    bool isAnagram(std::string s, std::string t) {
        int record[26];memset(record, 0, sizeof(int)*26);
        for (char ch: s) record[ch-'a'] ++;
        for (char ch: t){
            record[ch - 'a'] --;
            if (record[ch - 'a'] < 0) return false;
        }
        for (int i = 0; i<26; i++)
            if (record[i]) return false;
        return true;
    }
};
```     

## 第 7 天 链表         

### 141. 环形链表     
给定一个链表，判断链表中是否有环。          
如果链表中有某个节点，可以通过连续跟踪 next 指针再次到达，则链表中存在环。 为了表示给定链表中的环，我们使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 pos 是 -1，则在该链表中没有环。注意：pos 不作为参数进行传递，仅仅是为了标识链表的实际情况。      
如果链表中存在环，则返回 true 。 否则，返回 false 。     

示例 1：     
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/leetcode/leetcode141-1.png"></div>       

输入：head = [3,2,0,-4], pos = 1       
输出：true       
解释：链表中有一个环，其尾部连接到第二个节点。        

示例 2：      
输入：head = [1,2], pos = 0        
输出：true       
解释：链表中有一个环，其尾部连接到第一个节点。          

示例 3：       
输入：head = [1], pos = -1      
输出：false       
解释：链表中没有环。      

提示：     
* 链表中节点的数目范围是 [0, 104]       
* -105 <= Node.val <= 105      
* pos 为 -1 或者链表中的一个 有效索引 。        

