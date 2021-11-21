---
layout:     post
title:      Series Article of Leetcode Notes -- 10
subtitle:   Study Plan DS II 06-09 字符串      
date:       2021-11-16
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