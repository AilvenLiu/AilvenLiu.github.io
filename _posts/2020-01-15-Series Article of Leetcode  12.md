---
layout:     post
title:      Series Article of Leetcode Notes -- 12
subtitle:   Study Plan DS II 14 栈和队列      
date:       2021-11-29
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - leetcode      
    - c++      
    - data structure      
---     

> leetcode 刷题笔记，Study Plan Data Structure 2, Day 14 ： 栈和队列 。         

### 1249. 移除无效的括号            
给你一个由 '('、')' 和小写字母组成的字符串 s。          
你需要从字符串中删除最少数目的 '(' 或者 ')' （可以删除任意位置的括号)，使得剩下的「括号字符串」有效。          
请返回任意一个合法字符串。        

有效「括号字符串」应当符合以下 任意一条 要求：          
* 空字符串或只包含小写字母的字符串          
* 可以被写作 AB（A 连接 B）的字符串，其中 A 和 B 都是有效「括号字符串」           
* 可以被写作 (A) 的字符串，其中 A 是一个有效的「括号字符串」            
  

示例 1：        
输入：s = "lee(t(c)o)de)"        
输出："lee(t(c)o)de"       
解释："lee(t(co)de)" , "lee(t(c)ode)" 也是一个可行答案。           

示例 2：        
输入：s = "a)b(c)d"        
输出："ab(c)d"        

示例 3：       
输入：s = "))(("       
输出：""        
解释：空字符串也是有效的          

示例 4：        
输入：s = "(a(b(c)d)"           
输出："a(b(c)d)"         
 

提示：       
* 1 <= s.length <= 10^5          
* s[i] 可能是 '('、')' 或英文小写字母        

#### Thought         
这题其实思路很明确：建一个栈，字符串走两趟。          
第一趟：从前往后走，栈里存左括号（用以匹配合法的右货号），清除字符串里不合法的右括号。      
清空栈。         
第二趟：从后往前走，栈里存右括号（用以匹配合法的左括号），清除字符串里不合法的左括号。       

有一点需要注意，从前往后走的时候，如果 erase 删除了一个括号，记得将 index 减一；否则 idx 保持位置不动，但相应位置字符少了一个相当于后面字符整体前移， for 循环再执行一次 idx++，相当于把当前删除的字符后面的一位直接略过了。          

#### AC Version          
```c++         
class Solution {
public:
    string minRemoveToMakeValid(string s) {
        stack<char> st;
        for (int i = 0; i < s.size(); i++){
            if (s[i] == '(') st.push(s[i]);
            else if (s[i] == ')') {
                if (st.empty()) s.erase(i--, 1);
                // 注意这里的 i--。      
                else st.pop();
            }
        }
        st = stack<char>();
        for (int i = s.size()-1; i>=0; i--){
            if (s[i] == ')') st.push(s[i]);
            else if (s[i] == '('){
                if (st.empty()) s.erase(i, 1);
                else st.pop();
            }
        }
        return s;
    }
};
```            

### 1823. 找出游戏的获胜者
共有 n 名小伙伴一起做游戏。小伙伴们围成一圈，按 顺时针顺序 从 1 到 n 编号。确切地说，从第 i 名小伙伴顺时针移动一位会到达第 (i+1) 名小伙伴的位置，其中 1 <= i < n ，从第 n 名小伙伴顺时针移动一位会回到第 1 名小伙伴的位置。            

游戏遵循如下规则：          
1. 从第 1 名小伙伴所在位置 开始 。           
2. 沿着顺时针方向数 k 名小伙伴，计数时需要 包含 起始时的那位小伙伴。逐个绕圈进行计数，一些小伙伴可能会被数过不止一次。           
3. 你数到的最后一名小伙伴需要离开圈子，并视作输掉游戏。         
4. 如果圈子中仍然有不止一名小伙伴，从刚刚输掉的小伙伴的 顺时针下一位 小伙伴 开始，回到步骤 2 继续执行。           
5. 否则，圈子中最后一名小伙伴赢得游戏。          

给你参与游戏的小伙伴总数 n ，和一个整数 k ，返回游戏的获胜者。          

示例 1：
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/leetcode/leetcode1823.png"></div>       

输入：n = 5, k = 2
输出：3
解释：游戏运行步骤如下：           
1) 从小伙伴 1 开始。        
2) 顺时针数 2 名小伙伴，也就是小伙伴 1 和 2 。         
3) 小伙伴 2 离开圈子。下一次从小伙伴 3 开始。         
4) 顺时针数 2 名小伙伴，也就是小伙伴 3 和 4 。          
5) 小伙伴 4 离开圈子。下一次从小伙伴 5 开始。          
6) 顺时针数 2 名小伙伴，也就是小伙伴 5 和 1 。         
7) 小伙伴 1 离开圈子。下一次从小伙伴 3 开始。       
8) 顺时针数 2 名小伙伴，也就是小伙伴 3 和 5 。       
9) 小伙伴 5 离开圈子。只剩下小伙伴 3 。所以小伙伴 3 是游戏的获胜者。          


提示：       
* 1 <= k <= n <= 500       

#### Thought & AC         
一个模拟程序，没有什么难度。维护好三个变量：      
`start`：从哪个位置开始；          
`len`：当前队伍长度（用于取余保证 idx 合法性）;          
`thisPos`：哪个位置将被 erase。         

然后 while 循环走起即可。while 的判断条件是 peers.size()>1 。            

```c++         
class Solution {
public:
    int findTheWinner(int n, int k) {
        std::vector<int> peers;
        for (int i = 1; i <= n; i ++) peers.push_back(i);
        int start = 0, len = n;
        while(peers.size() > 1){
            int thisPos = (start + k - 1)%len;
            peers.erase(peers.begin()+thisPos);;
            --len;
            start = thisPos%len;
        }
        return peers[0];
    }
};
```        
