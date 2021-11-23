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

## 第 8 天 字符串          
### 49. 字母异位词分组
给你一个字符串数组，请你将 字母异位词 组合在一起。可以按任意顺序返回结果列表。      
字母异位词 是由重新排列源单词的字母得到的一个新单词，所有源单词中的字母都恰好只用一次。       

示例 1:           
输入: strs = ["eat", "tea", "tan", "ate", "nat", "bat"]        
输出: [["bat"],["nat","tan"],["ate","eat","tea"]]         

示例 2:       
输入: strs = [""]          
输出: [[""]]         

示例 3:         
输入: strs = ["a"]        
输出: [["a"]]      

提示：       
* 1 <= strs.length <= 104           
* 0 <= strs[i].length <= 100           
* strs[i] 仅包含小写字母         

#### Thought          
这道题，只需要构造两个数组，一个 vector<vector<string>> 二维字符串数组，和一个 vector<unordered_map<char, int>> 哈希映射数组，就迎刃而解了。      
说得再透彻一点，两个数组长度保持同步更新：二维字符串数组中的元素和哈希映射数组中的元素应当一一对应。比如， 哈希映射数组内现在只有一个元素（哈希映射），对应着 二维字符串数组内也只有一个元素（一维 vector）。在 strs 字符串数组的遍历过程中，对每一个字符串 str 都要构造一个哈希映射 map，然后遍历哈希映射数组，如果对新元素新构造的哈希映射和数组中某个现存的哈希映射元素（设下标为 j ），就证明当前遍历到的这个字符串可以被归类到 二维字符串数组中的下表为 j 的字符串数组中。        
否则，而为字符串数组和 哈希映射数组同时添加新元素（一维字符串数组和哈希映射）。      

#### AC Version        
```c++
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        vector<vector<string>> res;
        vector<unordered_map<char, int>> m;
        unordered_map<char, int> mtmp;
        for (char ch: strs[0])   mtmp[ch] ++;
        vector<string> stmp; stmp.emplace_back(strs[0]);
        m.emplace_back(mtmp);
        res.emplace_back(stmp);
        
        for (int i = 1; i < strs.size(); i++){
            unordered_map<char, int> mtmp;
            for (char ch: strs[i]) mtmp[ch] ++;
            bool flag = false;
            for (int j = 0; j < m.size(); j++){
                if (mtmp == m[j]){
                    res[j].emplace_back(strs[i]);
                    flag = true;
                }
            }
            if (flag) continue;
            vector<string> stmp; stmp.emplace_back(strs[i]);
            res.emplace_back(stmp);
            m.emplace_back(mtmp);
        }
        return res;
    }
};      
```      

### 43. 字符串相乘       
给定两个以字符串形式表示的非负整数 num1 和 num2，返回 num1 和 num2 的乘积，它们的乘积也表示为字符串形式。          

示例 1:     
输入: num1 = "2", num2 = "3"       
输出: "6"      

示例 2:      
输入: num1 = "123", num2 = "456"         
输出: "56088"       

说明：      
* num1 和 num2 的长度小于110。        
* num1 和 num2 只包含数字 0-9。         
* num1 和 num2 均不以零开头，除非是数字 0 本身。       
* 不能使用任何标准库的大数类型（比如 BigInteger）或直接将输入转换为整数来处理。       

#### Thought       
事实上就是 第六天 415题 字符串相加 的进阶。整个解应当包含两个主要函数和一个辅助函数：      
1. 字符串与整型数相乘。仿照字符串相加设计就可以，遍历字符串，通过 ch-'0' 将字符串中单个字符转为整型数，然后记录同参数中整型数相乘的值。注意结果可能是两位数，于是需要一个整型变量 add 记录进位值，一步一变；同理，当前位的乘积值也需要加上上一步的进位值（初始为 0 ）。记得在遍历结束后检查一下有没有溢出（超出原位数）的进位。          
2. 字符串加法。从后往前遍历两个字符串，通过 ch-'0' 将字符串中单个字符转为整型数，然后相加。要分 idx 都能取到、只能取到其中一个共三种情况讨论。依然要考虑到加和可能是两位数，通过一个整型变量 add 记录进位，一步一变；同理，当前位的的家和值也需要加上上一步的进位。记得在遍历结束后检查一下有没有溢出（超出原位数）的进位。          
3. multiChar。由于从十位开始的相乘结果后面要加上相应个数的零，但 c++ 又没有像 Python 那样重载 '*' 操作符。我们需要自己写一个 multiChar(const char& char, int n) 赋值函数，返回后面加上相应个数的零的结果字符串。        

于是答案呼之欲出：      
先排除两字符串有纯零的情况，直接返回。以其中任意一个字符串作为字符串整型数相乘函数的字符串参数，从后往前遍历另一个字符串（要 ch - '0' 转成整型数），记录字符串整型数相乘函数的结果，使用 multiChar 将后面加上相应个数的零。和当前结果 res 相加（于是 res 初始化当然为 "0"）。          

#### My AC Version         
```c++     
class Solution {
public:
    string multiply(string num1, string num2) {
        if (num1 == "0" || num2 == "0") return "0";
        string res = "0";
        int size1 = num1.size(), size2 = num2.size();
        for (int i = 0; i < size2; i++){
            string tmp = multiply(num1, int(num2[size2-1-i]-'0'));
            tmp = tmp + multiChar('0', i);
            res = addition(tmp, res);
        }
        return res;
    }
private:
    string multiply(string num1, int factor2){
        string res = ""; int add = 0;
        for (int i = 0; i < num1.size(); i++){
            int factor1 = num1[num1.size()-1-i] - '0';
            int tmp = factor1 * factor2;
            int thisPos = (add+tmp)%10;
            add = (add+tmp)/10;
            res = to_string(thisPos)+res;
        }
        if (add)
            res = to_string(add) + res;
        return res;
    }

    string addition(string num1, string num2){
        string res = "";    int add = 0;
        int i = num1.size()-1, j = num2.size()-1;
        while(i>=0 || j>=0){
            int val;
            if (i>=0 && j>=0)
                val = add + (num1[i--] - '0') + (num2[j--] - '0');
            else if (i < 0 && j>=0)
                 val = add + (num2[j--] - '0');
            else val = add + (num1[i--] - '0');
            if (val > 9){
                add = 1;
                val -= 10;
            }
            else add = 0;
            res = to_string(val) + res;
        }
        if (add)
            res = '1' + res;
        return res;
    }

    string multiChar(const char& ch, int n){
        string res = "";
        for (int i = 0; i < n; i++)
            res += ch;
        return res;
    }

};
```
