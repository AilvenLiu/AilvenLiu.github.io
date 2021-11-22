---
layout:     post
title:      Series Article of Leetcode Notes -- 10
subtitle:   Study Plan DS II 06-09 字符串      
date:       2021-11-21
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - leetcode      
    - c++      
    - data structure      
---     

> leetcode 刷题笔记，Study Plan Data Structure 2, Day 6 -- Day 9 ： 字符串 。         

## 第 6 天 字符串          

### 415. 字符串相加          
给定两个字符串形式的非负整数 num1 和num2 ，计算它们的和并同样以字符串形式返回。        
你不能使用任何內建的用于处理大整数的库（比如 BigInteger）， 也不能直接将输入的字符串转换为整数形式。          

示例 1：        
输入：num1 = "11", num2 = "123"          
输出："134"        

示例 2：         
输入：num1 = "456", num2 = "77"        
输出："533"         

示例 3：       
输入：num1 = "0", num2 = "0"       
输出："0"        

提示：           
* 1 <= num1.length, num2.length <= 104          
* num1 和num2 都只包含数字 0-9          
* num1 和num2 都不包含任何前导零           

#### Thought & AC         

这题也没什么意思，从两个字符串的各自的最后一位开始同步向前计算就可以了。通过 ch - '0' 可以将字符转为整型变量，另外加一个int 型变量 add 记录是否需要进位，一次一变。需要进位就置一，不需要就置零，反正相应位相加的时候要加一个这东西就成了。      
```c++        
class Solution {
public:
    string addStrings(string num1, string num2) {
        string res;
        int add = 0, i = num1.size()-1, j = num2.size()-1;
        while (i>=0 || j>=0){
            int val;
            if (i>=0 && j>=0) 
                val = (num1[i--] - '0') + (num2[j--] - '0') + add;
            else if (i>=0 && j < 0) val = num1[i--] - '0' + add;
            else val = num2[j--] - '0' + add; 
            if (val > 9){
                val -= 10;
                add = 1; 
            }else add = 0;
            res = to_string(val) + res;
        }
        if (add) res = '1' + res;
        return res;
    }
};
```      

### 409. 最长回文串        
给定一个包含大写字母和小写字母的字符串，找到通过这些字母构造成的最长的回文串。     
在构造过程中，请注意区分大小写。比如 "Aa" 不能当做一个回文字符串。        

示例 1:      
输入:        
"abccccdd"       
输出: 7          

解释:          
我们可以构造的最长的回文串是"dccaccd", 它的长度是 7。           

#### Thought         
先建立一个哈希映射存储每个字符出现的次数，遍历该哈希映射分两种情况讨论：        
1. 出现次数为偶数，则必然完全加入到字符串长度记录 res 构造为对称字符串。        
2. 出现次数为奇数，构造一个变量 maxOdd 记录出现次数最多的奇数字符个数。如果当前字符不时出现次数最多的奇数次字符，则需要舍掉一个成为偶数后加入到回文字符（长度记录） res；如果是，此前记录中的奇数次字符舍掉一个成为偶数加入到回文字符，maxOdd 更新为当前奇数次字符。       

for 循环遍历完后得到两个变量：res，纯偶数次字符组成的回文字符串长度； maxOdd，出现次数最多的奇数此字符个数。于是，两者相加，就是最终结果。            

#### My AC Version           
```c++       
class Solution {
public:
    int longestPalindrome(string s) {
        unordered_map<char, int> pool;
        for (char ch: s) pool[ch] ++;
        int res = 0, maxOdd = 0;
        for (auto it = pool.begin(); it != pool.end(); it++){
            printf("%c: %d\n", it -> first, it -> second);
            if (it -> second % 2 == 0) res += it -> second;
            else if (it -> second > maxOdd) {
                res += max(0, maxOdd-1);
                maxOdd = it -> second;
            } else 
                res += it -> second - 1;
        }
        return res+maxOdd;
    }
};
```        

## 第 7 天 字符串          

### 290. 单词规律         
给定一种规律 pattern 和一个字符串 str ，判断 str 是否遵循相同的规律。       
这里的 遵循 指完全匹配，例如， pattern 里的每个字母和字符串 str 中的每个非空单词之间存在着双向连接的对应规律。         

示例1:      
输入: pattern = "abba", str = "dog cat cat dog"       
输出: true          
 
示例 2:      
输入:pattern = "abba", str = "dog cat cat fish"       
输出: false        

示例 3:     
输入: pattern = "aaaa", str = "dog cat cat dog"       
输出: false     

示例 4:       
输入: pattern = "abba", str = "dog dog dog dog"          
输出: false       

说明:       
* 你可以假设 pattern 只包含小写字母， str 包含了由单个空格分隔的小写字母。             

#### Thought      
这题实际考察 unordered_map 映射。首先通过空格字符把一个字符串按照单词分割成由多个单词组成的 vector<string> 数组，确认 pattern 和数组的长度一致后，遍历 pattern 和数组使用 map<char, string> 将 pattern 中的字符和 vector 中的 string 联系起来。     
如果找到未出现在 map 中的 Pattern 字符，先查找 map 已存在的字符中有没有和当前 idx 对应的字符串，有的话说明存在不同的 pattern 字符对应同一个单词的情况，直接返回 false；否则，加入 char-string (字符单词对) 到 map。      
否则（遍历过程当前出现的 pattern 字符已经出现过，被存储到了 map），如果存储在 map 中的对应关系和当前 char-string 对应关系不一致，直接返回 false.           

#### AC Version           
```c++      
class Solution {
public:
    bool wordPattern(string pattern, string s) {
        vector<string> ss;
        int space = 0;
        for (int i = 0; i < s.size(); i++){
            if (s[i] == ' '){
                ss.emplace_back(s.substr(space, i-space));
                space = i+1;
            }
        }
        ss.emplace_back(s.substr( space, s.size()-space));

        if (pattern.size() != ss.size()) return false;

        unordered_map<char, string> p2s;
        for (int i = 0; i < pattern.size(); i++){
            if (p2s.find(pattern[i]) == p2s.end()){
                for (int j = 0; j < i; j++)
                    if (p2s[pattern[j]] == ss[i])
                        return false;
                p2s[pattern[i]] = ss[i];
            }
            else if (p2s[pattern[i]] != ss[i])
                return false;
        }
        return true;
    }
};
```       

### 763. 划分字母区间       
字符串 S 由小写字母组成。我们要把这个字符串划分为尽可能多的片段，同一字母最多出现在一个片段中。返回一个表示每个字符串片段的长度的列表。        

示例：             
输入：S = "ababcbacadefegdehijhklij"       
输出：[9,7,8]         
解释：划分结果为 "ababcbaca", "defegde", "hijhklij"。
每个字母最多出现在一个片段中。像 "ababcbacadefegde", "hijhklij" 的划分是错误的，因为划分的片段数较少。           
 

#### Thought          
找出每个字符首次和最后一次出现的 idx ，这个题就变成了合并区间形成最多不重复区间。        

#### My AC Version          
```c++         
class Solution {
public:
    vector<int> partitionLabels(string s) {
        vector<vector<int>> partition;
        vector<int> res;
        unordered_set<char> pool;
        for (int i = 0; i< s.size(); i++){
            char ch = s[i];
            if (pool.find(ch) == pool.end()){
                int last = s.rfind(ch);
                partition.emplace_back(vector<int>{i, last});
            }
        }
        int start = 0, end = partition[0][1];
        for (int i = 0; i < partition.size(); i++){
            if (partition[i][0] > end){
                res.emplace_back(end-start+1);
                start = partition[i][0];
                end  = partition[i][1];
            }
            else end = max(end, partition[i][1]);
        }
        res.emplace_back(end - start +1);
        return res; 
    }
};
```