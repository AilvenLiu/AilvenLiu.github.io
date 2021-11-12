---
layout:     post
title:      Series Article of Leetcode Notes -- 08
subtitle:   动态规划——股票买卖系列问题专题博客      
date:       2021-11-12
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - leetcode      
    - c++      
    - data structure      
---     

> leetcode 刷题笔记，Study Plan Data Structure 1, Day 8 -- Day 14 。         

## 第 8 天 链表      

### 206. 反转链表      
给你单链表的头节点 head ，请你反转链表，并返回反转后的链表。        

示例 1：         
输入：head = [1,2,3,4,5]       
输出：[5,4,3,2,1]      

示例 2：       
输入：head = [1,2]         
输出：[2,1]       

示例 3：        
输入：head = []       
输出：[]     

提示：        
* 链表中节点的数目范围是 [0, 5000]        
* -5000 <= Node.val <= 5000         

#### Thought       
迭代去做更简单高效，更易理解。遍历整个链表，使当前节点指向前一个，但又不希望丢失当前节点的 next 信息（丢失则显然无法继续遍历），于是需要一个 tmp 临时节点在使当前节点指向其前一节点之前保存其 next 节点信息。还需要考虑，第一个 pre 节点是什么？显然，head 节点最终要变成 tail 节点，它需要 next 指向 nullptr，于是第一个pre 就是 nullptr 了。      

#### My AC Version        
```c++        
/**

 * Definition for singly-linked list.

 * struct ListNode {

 *     int val;

 *     ListNode *next;

 *     ListNode() : val(0), next(nullptr) {}

 *     ListNode(int x) : val(x), next(nullptr) {}

 *     ListNode(int x, ListNode *next) : val(x), next(next) {}

 * };

 */
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if (!head || !head -> next) return head;
        ListNode* pre = nullptr;
        ListNode* cur = head;
        while(cur){
            ListNode* tmp = cur -> next;
            cur -> next = pre;
            pre = cur;
            cur = tmp;
        }
        return pre;
    }
};
```        
执行用时：8 ms, 在所有 C++ 提交中击败了43.97% 的用户    
内存消耗：8.1 MB, 在所有 C++ 提交中击败了59.03% 的用户       

### 83. 删除排序链表中的重复元素        
存在一个按升序排列的链表，给你这个链表的头节点 head ，请你删除所有重复的元素，使每个元素 只出现一次 。       
返回同样按升序排列的结果链表。       

示例 1：     
输入：head = [1,1,2]       
输出：[1,2]           

示例 2：      
输入：head = [1,1,2,3,3]        
输出：[1,2,3]          

提示：       
* 链表中节点数目在范围 [0, 300] 内        
* -100 <= Node.val <= 100      
* 题目数据保证链表已经按升序排列       

#### Thought        
这道题其实是 [203.移除链表元素](https://www.ouc-liux.cn/2021/11/03/Series-Article-of-Leetcode-06/#203-%E7%A7%BB%E9%99%A4%E9%93%BE%E8%A1%A8%E5%85%83%E7%B4%A0) 的演化，不同点有二：      
1. 由于是删除重复元素而不是特定元素，则原头节点必然会存在，不需要先查找头部元素了。    
2. 固定的特定值应当随 while 遍历每一步都变化为当前节点值，然后再套一层 while 判断当前节点 next 是否当被删除（是否和当前值相等）。       

#### My AC Version        

```c++      
/**

 * Definition for singly-linked list.

 * struct ListNode {

 *     int val;

 *     ListNode *next;

 *     ListNode() : val(0), next(nullptr) {}

 *     ListNode(int x) : val(x), next(nullptr) {}

 *     ListNode(int x, ListNode *next) : val(x), next(next) {}

 * };

 */

class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        if (head == nullptr || head -> next  == nullptr) return head;
        ListNode* node = head;
        while(node && node-> next){
            while(node -> next && node -> next -> val == node -> val)
                node -> next = node -> next -> next;
            node = node -> next;
        }
        return head;
    }
};
```

## 第 8 天 链表      

### 20. 有效的括号        
给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串 s ，判断字符串是否有效。
      
有效字符串需满足：       
* 左括号必须用相同类型的右括号闭合。        
* 左括号必须以正确的顺序闭合。

示例 1：      
输入：s = "()"      
输出：true    

示例 2：      
输入：s = "()[]{}"       
输出：true      

示例 3：        
输入：s = "(]"        
输出：false       

示例 4：      
输入：s = "([)]"       
输出：false         

示例 5：       
输入：s = "{[]}"        
输出：true        

#### Thought        
用栈去做，没什么好说的。       

#### My AC Version         

```c++       
class Solution {
public:
    bool isValid(string s) {
        stack<char> ss;
        for (int i = 0; i < s.size(); i++){
            char ch = s[i];
            if ((ch==')' || ch=='}' || ch==']') && ss.empty())
            return false;

            else if(ch == ']' && ss.top() == '[' ||
                    ch == '}' && ss.top() == '{' ||
                    ch == ')' && ss.top() == '(' )
                    ss.pop();
            
            else ss.push(ch);    
        }
        if (!ss.empty()) return false;
        else return true;
    }
};
```

### 232. 用栈实现队列

请你仅使用两个栈实现先入先出队列。队列应当支持一般队列支持的所有操作（push、pop、peek、empty）：      

实现 MyQueue 类：      
* void push(int x) 将元素 x 推到队列的末尾        
* int pop() 从队列的开头移除并返回元素        
* int peek() 返回队列开头的元素       
* boolean empty() 如果队列为空，返回 true ；否则，返回 false

说明：     
* 你只能使用标准的栈操作 —— 也就是只有 push to top, peek/pop from top, size, 和 is empty 操作是合法的。        
* 你所使用的语言也许不支持栈。你可以使用 list 或者 deque（双端队列）来模拟一个栈，只要是标准的栈操作即可。 

示例：     
输入：       
["MyQueue", "push", "push", "peek", "pop", "empty"]       
[[], [1], [2], [], [], []]      
输出：       
[null, null, null, 1, 1, false]       

解释：
```c++
MyQueue myQueue = new MyQueue();
myQueue.push(1); // queue is: [1]

myQueue.push(2); // queue is: [1, 2] (leftmost is front of the queue)

myQueue.peek(); // return 1

myQueue.pop(); // return 1, queue is [2]

myQueue.empty(); // return false

```      

提示：        
* 1 <= x <= 9        
* 最多调用 100 次 push、pop、peek 和 empty      
* 假设所有操作都是有效的 （例如，一个空的队列不会调用 pop 或者 peek 操作）


#### Thought       
设立两个栈，一出一进。队列 push 的时候正常得向 inStack push；执行 top() 或 peek() 操作的时候先判断 outStack 是否为空，是的话，将 inStack 的元素依次出栈加入到 outStack，此时 outStack pop 出的就是最先 push 到 inStack 的元素，也即先进先出 FIFO。     

#### My AC Version          
```c++      
class MyQueue {
private: 
    stack<int> instack;
    stack<int> outstack;
    void in2out(){
        while(!instack.empty()){
            outstack.push(instack.top());
            instack.pop();
        }
    }

public:
    MyQueue() {
    }
    ~MyQueue(){}
    
    void push(int x) {
        instack.push(x);
    }
    
    int pop() {
        if (outstack.empty())
            in2out();
        int x = outstack.top();
        outstack.pop();
        return x;
    }
    
    int peek() {
        if (outstack.empty())
            in2out();
        return outstack.top();
    }
    
    bool empty() {
        return instack.empty() && outstack.empty();
    }
};
```         

## 第 10 天 树       

今天就是常规树的前中后序遍历，用递归完成。         

#### My AC Version       
```c++        
class Solution {
public:
    // 前序       
    
    vector<int> preTraver;    
    vector<int> preorderTraversal(TreeNode* root) {
        if (root){
            preTraver.emplace_back(root -> val);
            preorderTraversal(root -> left);
            preorderTraversal(root -> right);
        }
        return preTraver;
    }

    // 中序
    
    vector<int> inTraver;
    vector<int> inorderTraversal(TreeNode* root) {
         if (root){
            inorderTraversal(root -> left);
            inTraver.emplace_back(root -> val);
            inorderTraversal(root -> right);
        }
        return inTraver;
    }

    // 后续

    vector<int> posTraver;
    vector<int> postorderTraversal(TreeNode* root) {
        if(root){
            postorderTraversal(root -> left);
            postorderTraversal(root -> right);
            posTraver.emplace_back(root -> val);
        }
        return posTraver;
    }
};
```

## 第 11 天 树       
### 102. 二叉树的层序遍历        
给你一个二叉树，请你返回其按 层序遍历 得到的节点值。 （即逐层地，从左到右访问所有节点）。          
 
示例：         
二叉树：[3,9,20,null,null,15,7],        
```
    3
   / \
  9  20
    /  \
   15   7
```       
返回其层序遍历结果：      
[[3]     
,[9,20]      
,[15,7]]     

#### Thought1       
有两种实现方案：队列广搜BFS 和 递归深搜DFS。     
用队列广搜的话，我的视线需要为每个 node 构造为 pair，使用一个额外的变量存储 node 的层数。再设置一个 vector<pair<TreeNode*, int>>，通过队列广搜，将所有的节点 pair 加入到容器。全部加入完毕后，再从 pair 类型容器中将元素一个个取出加入到 res。实现起来相当复杂。             

#### My AC Version1       
```c++     
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        if (!root) return {};
        vector<vector<int>> res;
        queue<pair<TreeNode*, int>> q1;
        vector<pair<TreeNode*, int>> q2;
        q1.push(make_pair(root, 0));
        q2.push_back(make_pair(root, 0));
        while(!q1.empty()){
            pair<TreeNode*, int> tmp = q1.front();
            q1.pop();
            if (get<0>(tmp) -> left){
                q1.push(make_pair(get<0>(tmp) -> left, get<1>(tmp)+1));
                q2.push_back(make_pair(get<0>(tmp) -> left, get<1>(tmp)+1));
            }
            if (get<0>(tmp) -> right){
                q1.push(make_pair(get<0>(tmp) -> right, get<1>(tmp)+1));
                q2.push_back(make_pair(get<0>(tmp) -> right, get<1>(tmp)+1));
            }
        }
        vector<int> tmp;
        int pre = 0;
        tmp.emplace_back(root -> val);
        for (int i = 1; i < q2.size(); i++){
            int idx = get<1>(q2[i]);
            TreeNode* node = get<0>(q2[i]);
            if (idx != pre){
                res.emplace_back(tmp);
                tmp.clear();
            }
            tmp.emplace_back(node -> val);
            pre = idx;
        }
        res.emplace_back(tmp);
        return res;
    }
};
```