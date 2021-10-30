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

### 62. Unique Paths       
A robot is located at the top-left corner of a m x n grid (marked 'Start' in the diagram below).         
The robot can only move either down or right at any point in time. The robot is trying to reach the bottom-right corner of the grid (marked 'Finish' in the diagram below).         
How many possible unique paths are there?      

Example 1:      
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/leetcode/leetcode062.png"></div>       
Input: m = 3, n = 7          
Output: 28            

Example 2:        
Input: m = 3, n = 2           
Output: 3          
Explanation:           
From the top-left corner, there are a total of 3 ways to reach the bottom-right corner:        
1. Right -> Down -> Down      
2. Down -> Down -> Right      
3. Down -> Right -> Down       

Example 3:      
Input: m = 7, n = 3         
Output: 28           

Example 4:        
Input: m = 3, n = 3        
Output: 6         

Constraints:        
* 1 <= m, n <= 100              
* It's guaranteed that the answer will be less than or equal to 2 * 109.          

这道题不难，典型的动态规划。由于每个格子只能尤其上面或左面的格子到达，于是每个格子的 unique path 就等于其左面和上面的格子的 unique path 的加和。特别的，最上面一排和最左面一列格子的 unique path 等于一。从而，建立一个矩阵，从第 （1,1） 个格子开始遍历就好了，最后返回右下角格子的值。      
这道题可以继续优化其空间性能。注意到，当前格子的值仅有其左边和上面的格子决定，一个自然而然的想法是设定三个变量将空间复杂度降到 O(1) 。但实际情况是，这是个二维的动态，每次要更新三个值，我们很难通过固定数目的几个变量更新有二维方位关系的变量。但退而求其次，又观察到，在更新第 k 行数据时，只使用了 k / k-1 行的数据，跟之前的行没有关系（列也行，这里选行），从而，我们使用两个行向量迭代更新，将复杂度降到 O(n) 级别。           

#### My AC Version         
```c++      
class Solution {
public:
    int uniquePaths(int m, int n) {
        vector<int> cur(n, 1), pre(n, 1);
        for (int i = 1; i < m; i ++){
            for (int j = 1; j < n; j++)
                cur[j] = cur[j-1] + pre[j];
            pre = cur;
            cur.assign(n,1);
        }
        return pre[n-1];
    }
};
```      
Runtime: 0 ms, faster than 100.00% of C++ online submissions for Unique Paths.      
Memory Usage: 6.1 MB, less than 50.65% of C++ online submissions for Unique Paths.         

## Day 13 Dynamic Programming           
[GitHub 连接](https://github.com/OUCliuxiang/leetcode/blob/master/StudyPlan/Algo2/day13)          

### 5. Longest Palindromic Substring          
Given a string s, return the longest palindromic substring in s.      

Example 1:      
Input: s = "babad"         
Output: "bab"       
Note: "aba" is also a valid answer.         

Example 2:      
Input: s = "cbbd"           
Output: "bb"          

Example 3:       
Input: s = "a"           
Output: "a"         

Example 4:          
Input: s = "ac"          
Output: "a"        

Constraints:           
* 1 <= s.length <= 1000            
* s consist of only digits and English letters.          

最长回文子串，经典题目了。这题不用动态做，动态规划是反人类的解法，根本想不明白。这题用中心扩散法，遍历一遍字符串，从当前位置向后寻找连续相同字符，并从相同字符的首尾两端继续向外扩散，寻找相同字符。注意，连续相同字符必是回文子串，所以一定要先找出最长连续相同字符子串，在此基础上再向外扩散。           

#### My AC Version        
```c++       
class Solution {
public:
    string longestPalindrome(string s) {
        int len = s.size();
        if (len <= 1) return s;
        
        int subLen = 1, subStart = 0;
        int i = 0;
        while(i < len){
            int start = i, end = i;
            while( end + 1 < len && s[end+1] == s[end]) end++; 
            while( end + 1 < len && start > 0 && s[end+1] == s[start-1]){end++;start--;}
            if(end-start+1 > subLen){
                subLen = end-start+1;
                subStart = start;
            }
            i++;
        }
        return s.substr(subStart, subLen);
    }
};
```         
Runtime: 18 ms, faster than 84.49% of C++ online submissions for Longest Palindromic Substring.         
Memory Usage: 7.1 MB, less than 82.88% of C++ online submissions for Longest Palindromic Substring.            

### 413. Arithmetic Slices         
An integer array is called arithmetic if it consists of at least three elements and if the difference between any two consecutive elements is the same.       
For example, [1,3,5,7,9], [7,7,7,7], and [3,-1,-5,-9] are arithmetic sequences.       

Given an integer array nums, return the number of arithmetic subarrays of nums.         
A subarray is a contiguous subsequence of the array.         

Example 1:       
Input: nums = [1,2,3,4]           
Output: 3          
Explanation: We have 3 arithmetic slices in nums: [1, 2, 3], [2, 3, 4] and [1,2,3,4] itself.          

Example 2:      
Input: nums = [1]        
Output: 0         

Constraints:         
* 1 <= nums.length <= 5000           
* -1000 <= nums[i] <= 1000         

等比数列，这题也是建立了一个 dp 数组，但这道题的解没有那么强的动态规划的味道。      
#### My AC Version      
```c++         
class Solution {
public:
    int numberOfArithmeticSlices(vector<int>& nums) {
        int len = nums.size();
        if (len<3) return 0;
        int count = 0;
        int dp[len];
        memset(dp, 0, sizeof(int)*len);
        
        for (int i = 2; i < len; i++){
            if (nums[i-1] - nums[i-2] == nums[i] - nums[i-1]) 
                dp[i] = dp[i-1] + 1;
            count += dp[i];
        }
        return count;
    }
};
```         
Runtime: 0 ms, faster than 100.00% of C++ online submissions for Arithmetic Slices.      
Memory Usage: 7.4 MB, less than 53.84% of C++ online submissions for Arithmetic Slices.        

建立一个和原数组等长的 dp 数组用以存储以第 i 个元素为结尾的等比数列的个数：显然，在 `dp[i+1] - dp[i] == dp[i] - dp[i-1]` 的前提下， `dp[i+1] = dp[i] + 1`，后一个元素收尾的等比数列数量完全继承前一个，并比前一个多出一个内容为 `[i-1, i, i+1]` 的等比数列。则最终的结果就是 dp 数组诸值的加和。          

## Day 14 Dynamic Programming           
[GitHub 连接](https://github.com/OUCliuxiang/leetcode/blob/master/StudyPlan/Algo2/day14)          

### 91. Decode Ways       
A message containing letters from A-Z can be encoded into numbers using the following mapping:      
* 'A' -> "1"        
* 'B' -> "2"        
* ...           
* 'Z' -> "26"

To decode an encoded message, all the digits must be grouped then mapped back into letters using the reverse of the mapping above (there may be multiple ways). For example, "11106" can be mapped into:          
* "AAJF" with the grouping (1 1 10 6)         
* "KJF" with the grouping (11 10 6)        

Note that the grouping (1 11 06) is invalid because "06" cannot be mapped into 'F' since "6" is different from "06".          
Given a string s containing only digits, return the number of ways to decode it.        
The answer is guaranteed to fit in a 32-bit integer.          

Example 1:       
Input: s = "12"       
Output: 2        
Explanation: "12" could be decoded as "AB" (1 2) or "L" (12).       

Example 2:        
Input: s = "226"       
Output: 3       
Explanation: "226" could be decoded as "BZ" (2 26), "VF" (22 6), or "BBF" (2 2 6).          

Example 3:           
Input: s = "0"           
Output: 0         
Explanation: There is no character that is mapped to a number starting with 0.         
The only valid mappings with 0 are 'J' -> "10" and 'T' -> "20", neither of which start with 0.          
Hence, there are no valid ways to decode this since all digits need to be mapped.            

Example 4:       
Input: s = "06"        
Output: 0      
Explanation: "06" cannot be mapped to "F" because of the leading zero ("6" is different from "06").          


数字编码方式，这题正常人想不出来怎么解。          

#### My AC Version          
```c++       
class Solution {
public:
    int numDecodings(string s) {
        if(s[0] == '0') return 0;
        int len = s.size();
        int count = 1, pre = 1, ppre=1;
        for (int i = 1; i < len; i++){
            if(s[i] == '0' && (s[i-1] == '0' || s[i-1] > '2')) 
                return 0;
            if(s[i] == '0' && (s[i-1] == '1' || s[i-1] =='2')) 
                count = ppre;
            
            if(s[i] != '0') count = pre;
            if(s[i] != '0' && s[i-1] != '0' && 
               10*(s[i-1]-'0') + s[i]-'0' <= 26) count += ppre;
            
            ppre = pre;
            pre = count;
        }       
        return count;
    }
};
```          
Runtime: 0 ms, faster than **100.00%** of C++ online submissions for Decode Ways.       
Memory Usage: 6.2 MB, less than 70.64% of C++ online submissions for Decode Ways.           
要用动态解这个题，明确两点：        
1. 截至第 i 个位置，编码方式之和（count）只和前两个位置（ppre，pre）有关；         
2. '0' 是解题的关键。       

我们看这个解，由于 ‘0’ **只能** 和前面一个数字组成一个两位数（10/20）被解码，先把它拿出来讨论。       
1. 当整个串的首字符就是 ‘0’，无法被解码，直接返回 0；否则进入循环跳到一下选择中。          
2. 从第 1 个元素开始，当第 i 个元素是 ‘0’，判断其前一个元素能否和 ‘0’ 组成合法的可编码两位数，否，则直接返回 0。        
3. 当第 i 个元素是 ‘0’，且前一个元素为 ‘1’或‘2’ ，和 ‘0’ 组成了一个合法的两位数，则至此为止的编码方式**只能**是在截至向前数第二个元素的编码后面加一个字符，从而 `count = ppre`。         
4. 当第 i 个元素非 ‘0’，则无论之前一个元素是什么，截至当前元素的编码都可以在截至上一个元素的编码后加一个字符。也即，编码方式总和继承上一个元素，先来一个 `count = pre`。        
5. 然后再判断前一个元素是否能和当前元素组成一个合法的可被编码两位数。如果能，总和在上一步基础上再加一个截止到上上个元素的总和（从截止到上上个元素的编码再加一个两位数编码的字符），`count += ppre`。       
6. 结束系列判断后 做一个 update，`ppre = pre; pre = count;` 继续 for 循环。       


### 139. Word Break      
Given a string s and a dictionary of strings wordDict, return true if s can be segmented into a space-separated sequence of one or more dictionary words.      
Note that the same word in the dictionary may be reused multiple times in the segmentation.         

Example 1:         
Input: s = "leetcode", wordDict = ["leet","code"]          
Output: true          
Explanation: Return true because "leetcode" can be segmented as "leet code".         

Example 2:          
Input: s = "applepenapple", wordDict = ["apple","pen"]          
Output: true           
Explanation: Return true because "applepenapple" can be segmented as "apple pen apple".        
Note that you are allowed to reuse a dictionary word.         

Example 3:        
Input: s = "catsandog", wordDict = ["cats","dog","sand","and","cat"]
Output: false        

Constraints:          
* 1 <= s.length <= 300          
* 1 <= wordDict.length <= 1000           
* 1 <= wordDict[i].length <= 20           
* s and wordDict[i] consist of only lowercase English letters.        
* All the strings of wordDict are unique.      

又一个凡人根本解不出来的题目。讨论区的动态规划解的思路是这样：遍历字符串，从当前字符向前 for 循环查找到当前之前最长模式长度完成匹配的子串的最后字符的后一个字符到当前字符是否能在 wodrDict 中找到相匹配的子串。是的话，把匹配到的子串的最后一个字符对应位置的 dp 数组值置 true，向后寻找。           
最后一个字符对应位置的 dp 数组值，就对应着直到最后一个字符，是否匹配成功。        
为了高效 find 查找，显然应当将存储在 vector 容器中的 word 放置到有 hash 关系的 unordered_set 容器中。       
另，不知道算不算一个技巧，将 dp 数组的长度设定为待匹配字符串长度+1，并 dp[i] 代表直到第 i 个字符 str[i-1] 是否有被匹配；dp[0] 置 true。        

#### My AV Version         
```c++        
class Solution {
public:
    bool wordBreak(string s, vector<string>& wordDict) {
        unordered_set<string> wordSet;
        int maxLen = 0, len = s.size();
        for(int i = 0; i < wordDict.size(); i++){
            string word = wordDict[i];
            wordSet.emplace(word);
            maxLen = max(maxLen, (int)word.size());
        }
        bool dp[len+1];
        memset(dp, false, sizeof(bool)*(len+1));
        dp[0] = true;
        
        for (int i = 0; i < len; i++){
            for (int j = i; j >= 0 && i-j < maxLen; j--){
                if(dp[j] && wordSet.find(s.substr(j,i-j+1))!=wordSet.end()){
                    dp[i+1] = true;
                    break;
                }
            }
        }
        return dp[len];
    }
};
```        
Runtime: 0 ms, faster than **100.00%** of C++ online submissions for Word Break.         
Memory Usage: 7.6 MB, less than **96.00%** of C++ online submissions for Word Break.          

## Day 15 Dynamic Programming           
[GitHub 连接](https://github.com/OUCliuxiang/leetcode/blob/master/StudyPlan/Algo2/day15)          

### 300. Longest Increasing Subsequence          
Given an integer array nums, return the length of the longest strictly increasing subsequence.       
A subsequence is a sequence that can be derived from an array by deleting some or no elements without changing the order of the remaining elements. For example, [3,6,2,7] is a subsequence of the array [0,3,1,6,2,2,7].        

Example 1:         
Input: nums = [10,9,2,5,3,7,101,18]           
Output: 4         
Explanation: The longest increasing subsequence is [2,3,7,101], therefore the length is 4.           

Example 2:         
Input: nums = [0,1,0,3,2,3]       
Output: 4         

Example 3:         
Input: nums = [7,7,7,7,7,7,7]          
Output: 1        

Constraints:          
* 1 <= nums.length <= 2500           
* -104 <= nums[i] <= 104         

最长上升子序列，也是个经典题目了。用动态去做，建立一个和 nums 数组等长的 dp 数组存储直到第 i(start @ 0) 个数的最长上升子序列的长度。更新方式则是从当前向前遍历寻找出 nums 小于当前值的最大 dp 值 dpi，当前位置 dp 值则为 dp[i] = dpi+1。 实时更新最大 dp 值。        

不会的看了解析还是不会，会了的多做几遍也没什么进步，嘿。         

#### My AC Version          
```c++        
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int len = nums.size();
        if(len == 1) return 1;
        
        int dp[len], res = 0;
        for (int i = 0; i < len; i++) dp[i]=1;
        for (int i = 1; i < len; i++){
            int dpi = 0;
            for (int j = i-1; j >= 0; j--)
                if(nums[i]>nums[j])
                    dpi = max(dpi, dp[j]);
            if(dpi>0) dp[i] += dpi;
            res = max(res, dp[i]);
        }
        return res;
    }
};
```         
Runtime: 345 ms, faster than 24.58% of C++ online submissions for Longest Increasing Subsequence.         
Memory Usage: 10.2 MB, less than 99.92% of C++ online submissions for Longest Increasing Subsequence.          


### 673. Number of Longest Increasing Subsequence         
Given an integer array nums, return the number of longest increasing subsequences.        
Notice that the sequence has to be strictly increasing.        

Example 1:        
Input: nums = [1,3,5,4,7]         
Output: 2           
Explanation: The two longest increasing subsequences are [1, 3, 4, 7] and [1, 3, 5, 7].        

Example 2:       
Input: nums = [2,2,2,2,2]          
Output: 5            
Explanation: The length of longest continuous increasing subsequence is 1, and there are 5 subsequences' length is 1, so output 5.       

Constraints:           
* 1 <= nums.length <= 2000          
* -106 <= nums[i] <= 106          

上一题的进阶版本，解法也简单。除了用来存储直到当前 idx 的最长上升子序列长度的 dp 数组外，另建一个 cnt 数组存储达到当前 idx 的最长上升子序列的个数。按照讨论区，为了同步更新 dp 和 cnt，这道题我们更新状态数组的方向应当以正序。特别的，无论对 dp 还是 cnt 数组，要分两种情况进行讨论：1. dp[pre]+1 > dp[cur] ，这种情况更新 dp[cur]=dp[pre]+1，cnt当前状态继承前一状态 cnt[cur] = cnt[pre]； 2. dp[pre]+1 == dp[cur]，这种情况 dp 不发生改变，cnt 的选择增加为 cnt[cur] += cnt[pre] 。         
每个 idx 更新完毕要判断一下最长上上子序列长度有没有变化，有变化的话当前长度和可选择个数都要重新赋值；没有变化且当前 dp[i] 等于当前最长长度，count += cnt[i] 。       
#### My AC Version             
```c++          
class Solution {
public:
    int findNumberOfLIS(vector<int>& nums) {
        int len = nums.size();
        if (len == 1) return 1; 
        int dp[len], cnt[len], count=0, maxLen = 0;

        for (int i = 0; i < len; i ++){
            dp[i] = 1; cnt[i] = 1;
            for (int j = 0; j < i; j ++){
                if(nums[j] < nums[i]){
                    if(dp[j]+1 > dp[i]){
                        dp[i] = dp[j]+1;
                        cnt[i] = cnt[j];
                    }else if(dp[j]+1 == dp[i])
                        cnt[i] += cnt[j];
                }
            }
            if(dp[i]>maxLen){
                maxLen = dp[i];
                count = cnt[i];
            }else if(dp[i] == maxLen)
                count += cnt[i];
        }
        return count;
    }
};
```          
Runtime: 174 ms, faster than 37.16% of C++ online submissions for Number of Longest Increasing Subsequence.         
Memory Usage: 12.7 MB, less than 97.42% of C++ online submissions for Number of Longest Increasing            