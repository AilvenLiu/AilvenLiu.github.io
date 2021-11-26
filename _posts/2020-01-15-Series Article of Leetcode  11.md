---
layout:     post
title:      Series Article of Leetcode Notes -- 11
subtitle:   Study Plan DS II 10-13 链表      
date:       2021-11-25
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - leetcode      
    - c++      
    - data structure      
---     

> leetcode 刷题笔记，Study Plan Data Structure 2, Day 10 -- Day 13 ： 链表 。         

## 第 10 天 链表          

### 2. 两数相加          
给你两个 非空 的链表，表示两个非负的整数。它们每位数字都是按照 逆序 的方式存储的，并且每个节点只能存储 一位 数字。      
请你将两个数相加，并以相同形式返回一个表示和的链表。       
你可以假设除了数字 0 之外，这两个数都不会以 0 开头。       

示例 1：      
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/leetcode/leetcode002.jpg"></div>       

输入：l1 = [2,4,3], l2 = [5,6,4]        
输出：[7,0,8]      
解释：342 + 465 = 807.        

示例 2：       
输入：l1 = [0], l2 = [0]        
输出：[0]          

示例 3：        
输入：l1 = [9,9,9,9,9,9,9], l2 = [9,9,9,9]         
输出：[8,9,9,9,0,0,0,1]           

提示：         
* 每个链表中的节点数在范围 [1, 100] 内         
* 0 <= Node.val <= 9           
* 题目数据保证列表表示的数字不含前导零         

#### Thought     
这题和 字符串相加 非常相似。由于链表中数字逆序，也即头节点是最低位，尾节点是最高位，更方便我们进位操作：要设置一个 int 变量 add 记录进位，初始化为 0。当 l1 存在，l2 存在，或 add 存在，三者满足任意一者，进入 while 循环。        
然后分四种情况讨论：        
1. l1, l2 都存在。计算出当前位加和 val 为 两节点值加 add。新的 add 相应为 val /10 ，val 自然也通过 %10 变为个位数字。l1, l2 各自走到自己的 next。            
2. l1 存在，l2 不存在。同上，但不再操作 l2。      
3. l1 不存在，l2 存在。同上，但不再操作 l1。        
4. else 情形，此时 l1, l2 都不存在，但 add 为 1，也即现存位的加和计算结束后，有溢出的进位。此时 node -> val 直接置为 add 即可。 **注意**，千万不要忘记在 node -> val 赋值后，重新置零 val，否则会陷入死循环。        

最后考虑给 node 是否需要走到下一位，事实上由于 l1, l2, add 都已经完成更新，此时判断 node 是否需要再加一位的条件，和 while 循环可进入的条件一致：当 l1 存在，l2 存在，或 add 存在，三者满足任意一者，node 需要往后走一位。当然要先 new 一个 next 空节点。     

#### AC Version    
```c++    
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode* head = new ListNode(0);
        ListNode* node = head;
        int add = 0;
        while(l1 || l2 || add){
            if (l1 && l2) {
                int val = l1 -> val + l2 -> val + add;
                add = val/10; val %= 10;
                node -> val = val; 
                l1 = l1 -> next; 
                l2 = l2 -> next;
            }
            else if (l1 && !l2){
                int val = l1 -> val + add;
                add = val/10; val %= 10;
                node -> val = val;
                l1 = l1 -> next; 
            }
            else if (!l1 && l2){
                int val = l2 -> val + add;
                add = val/10; val %= 10;
                node -> val = val;
                l2 = l2 -> next; 
            } 
            else {
                node -> val = add;
                add = 0;
            }

            if (l1 || l2 || add){
                node -> next = new ListNode(0);
                node = node -> next;
            }
        }
        return head;
    }
};
```

### 142. 环形链表 II       
给定一个链表，返回链表开始入环的第一个节点。 如果链表无环，则返回 null。     
如果链表中有某个节点，可以通过连续跟踪 next 指针再次到达，则链表中存在环。 为了表示给定链表中的环，评测系统内部使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。如果 pos 是 -1，则在该链表中没有环。注意：pos 不作为参数进行传递，仅仅是为了标识链表的实际情况。     
不允许修改 链表。     

示例 1：      
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/leetcode/leetcode002.jpg"></div>        

输入：head = [3,2,0,-4], pos = 1       
输出：返回索引为 1 的链表节点       
解释：链表中有一个环，其尾部连接到第二个节点。         

示例 2：       
输入：head = [1,2], pos = 0         
输出：返回索引为 0 的链表节点       
解释：链表中有一个环，其尾部连接到第一个节点。     

示例 3：       
输入：head = [1], pos = -1       
输出：返回 null       
解释：链表中没有环。         
 
提示：        
* 链表中节点的数目范围在范围 [0, 104] 内            
* -105 <= Node.val <= 105         
* pos 的值为 -1 或者链表中的一个有效索引

#### Thought & AC Version          
只要能想到用 哈希集合 存储节点，一切都迎刃而解：      
建一个哈希表，开始节点遍历。没新访问到一个节点，先判断能否在 哈希集合 中中找到该节点。能，则立刻返回；否则，添加该节点到 哈希集合 ，走到下一个节点。     
如果顺利走完所有节点而没有返回，即意味着没有环。返回 nullptr.          

```c++
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        unordered_set<ListNode*> pool;
        while(head){
            if (pool.find(head) != pool.end())
                return head;
            pool.insert(head); 
            head = head -> next;
        }
        return nullptr;
    }
};
```           

## 第 11 天 链表            

### 160. 相交链表           
给你两个单链表的头节点 headA 和 headB ，请你找出并返回两个单链表相交的起始节点。如果两个链表不存在相交节点，返回 null 。          
图示两个链表在节点 c1 开始相交：
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/leetcode/leetcode160.png"></div>        

注意，函数返回结果后，链表必须 保持其原始结构 。        

#### Thought & AC            
使用 unordered_set 哈希集合存储一个链表的所有节点，遍历另一个节点。如果遍历过程中在集合中找到了当前节点，既是相交，返回节点。若遍历完整结束未返回，既是没有相交节点，返回 Nullptr。       

```c++     
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        unordered_set<ListNode*> nodeSet;
        while (headA) {nodeSet.insert(headA); headA = headA -> next;}
        while(headB){
            if (nodeSet.find(headB) != nodeSet.end()) return headB;
            headB = headB -> next; 
        }return nullptr;
    }
};
```