---
layout:     post
title:      Series Article of Leetcode Notes -- 08
subtitle:   Study Plan DS I 08-14      
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
// Definition for singly-linked list.           
// struct ListNode {            
//     int val;          
//     ListNode *next;          
//     ListNode() : val(0), next(nullptr) {}           
//     ListNode(int x) : val(x), next(nullptr) {}         
//     ListNode(int x, ListNode *next) : val(x), next(next) {}       
// };         

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
// Definition for singly-linked list.           
// struct ListNode {           
//     int val;          
//     ListNode *next;         
//     ListNode() : val(0), next(nullptr) {}          
//     ListNode(int x) : val(x), next(nullptr) {}         
//     ListNode(int x, ListNode *next) : val(x), next(next) {}         
// };          

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
用队列广搜的话，我的实现需要为每个 node 构造为 pair，使用一个额外的变量存储 node 的层数。再设置一个 vector<pair<TreeNode*, int>>，通过队列广搜，将所有的节点 pair 加入到容器。全部加入完毕后，再从 pair 类型容器中将元素一个个取出加入到 res。实现起来略复杂了些。             

#### My AC Version 1       
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

#### Thought2      
但实际上广搜实现方案可以更简便的使用一个 int 变量记录每层元素的数量，然后通过 for 循环一次性 pop 出一整层的 node，而不必通过 pair 这样复杂的操作记录当前在第几层。         

#### My AC Version 2       
```c++
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> res;
        if (!root) return res;
        queue<TreeNode*> q;
        q.push(root);
        int count = 1;
        while(!q.empty()){
            vector<int> tmp;
            int count_ = 0;
            for (int i = 0; i < count; i++){
                TreeNode* node = q.front();
                q.pop();
                tmp.emplace_back(node -> val);
                if (node -> left) {count_ ++; q.push(node -> left);}
                if (node -> right) {count_++; q.push(node -> right);}
            }
            count = count_;
            res.emplace_back(tmp);
        }
        return res;
    }
};
```

#### Thought3         

这道题，其实天然适合用深搜去做：给定一个初始 layer = 0，每递归调用一次深搜函数，其实都是再往下走一层，也即给出参数为 layer+1。递归函数内考虑 layer == res.size() 的情况：res 中尚没有当前层记录，需要先手动扩容 push 进去一个新的空 vector<int> 数组。       

#### My AC Version 3
```c++
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> res;
        if (!root) return res;
        resGen(res, root, 0);
        return res;
    }
private:
    void resGen(vector<vector<int>>& res, TreeNode* node, int layer){
        if (layer == res.size()){
            vector<int> t;
            res.emplace_back(t);
        }
        res[layer].emplace_back(node -> val);
        if (node -> left) resGen(res, node -> left, layer + 1);
        if (node ->right) resGen(res, node ->right, layer + 1);
    }
};
```     

### 104. 二叉树的最大深度         
给定一个二叉树，找出其最大深度。       
二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。        
说明: 叶子节点是指没有子节点的节点。       

示例：      
给定二叉树 [3,9,20,null,null,15,7]，          
```
    3
   / \
  9  20
    /  \
   15   7
```
返回它的最大深度 3 。          

#### Thought & AC 1        

可以跟上一题一样用有层数控制（每层 node 数量变量）的 BFS 广搜。       

```c++    
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if (!root) return 0;
        int maxLen = 0, count = 1;
        queue<TreeNode*> q;
        q.push(root);
        while(!q.empty()){
            maxLen ++;
            int count_ = 0;
            for (int i = 0 ;i < count; i ++){
                TreeNode* tmp = q.front();
                q.pop();
                if (tmp -> left){count_++; q.push(tmp->left);}
                if (tmp ->right){count_++; q.push(tmp->right);}
            }
            count = count_;
        }
        return maxLen;
    }
};
```     

#### Thought & AC 2       
也可以用深搜，且由于是求深度信息，深搜应当更加直观。      

```c++
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if (!root) return 0;
        int ans = 1;
        resolv(root, 1, ans);
        return ans;
    }
private:
    void resolv(TreeNode* node, int curDep, int& maxDep){
        if(!node) return;
        maxDep = max(maxDep, curDep);
        resolv(node -> left, curDep+1, maxDep);
        resolv(node-> right, curDep+1, maxDep);
    }
};
```       
执行用时：4 ms, 在所有 C++ 提交中击败了95.07% 的用户         
内存消耗：18.3 MB, 在所有 C++ 提交中击败了95.25% 的用户       

### 101. 对称二叉树       
给定一个二叉树，检查它是否是镜像对称的。        
例如，二叉树 [1,2,2,3,4,4,3] 是对称的。         
```
    1
   / \
  2   2
 / \ / \
3  4 4  3
```       
但是下面这个 [1,2,2,null,3,null,3] 则不是镜像对称的:       
```
    1
   / \
  2   2
   \   \
   3    3
```         

#### Thought      
非常自然的想法是用递归：一个树对称，当且仅当其左子树和右子树成镜像对称：       
1. 当左右子树均为空，镜像对称；        
2. 有一个非空，非对称；      
3. 均非空，节点值不相等，非对称；     
4. 节点值相等，当左子树的左分支与右子树的右分支镜像对称，且，左子树的右分支与右子树的左分支镜像对称。      

#### My AC Version        
```c++    
class Solution {
public:
    bool isSymmetric(TreeNode* root) {
        if (!root) return true;
        else return isSymmetric(root -> left, root -> right); 
    }
private:
    bool isSymmetric(TreeNode* le, TreeNode* ri){
        if (!le && !ri) return true;
        if (!le || !ri) return false;
        if (le -> val == ri -> val) 
            return isSymmetric(le->left, ri->right) &&
                   isSymmetric(le->right, ri->left);
        else return false;
    }
};
```        

## 第 12 天 树       

### 226. 翻转二叉树        
翻转一棵二叉树。       
示例：     
输入：
```
     4
   /   \
  2     7
 / \   / \
1   3 6   9
```
输出：      
```
     4
   /   \
  7     2
 / \   / \
9   6 3   1
```      

#### Thought & AC      
没什么说的，递归。     
```c++       
class Solution {
public:
    TreeNode* invertTree(TreeNode* root) {
        invertNode(root);
        return root;
    }
private:
    void invertNode(TreeNode* node){
        if (!node) return;
        TreeNode* tmp = node -> left;
        node -> left = node -> right;
        node -> right = tmp;
        invertNode(node -> left);
        invertNode(node -> right);
    }
};
```      
执行用时：4 ms, 在所有 C++ 提交中击败了59.01% 的用户        
内存消耗：9.4 MB, 在所有 C++ 提交中击败了87.74% 的用户       

### 112. 路径总和        
给你二叉树的根节点 root 和一个表示目标和的整数 targetSum ，判断该树中是否存在 根节点到叶子节点 的路径，这条路径上所有节点值相加等于目标和 targetSum 。        
叶子节点 是指没有子节点的节点。       

示例 1：       
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/leetcode/leetcode112-1.jpg"></div>       

输入：root = [5,4,8,11,null,13,4,7,2,null,null,null,1], targetSum = 22        
输出：true        

示例 2：     
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/leetcode/leetcode112-2.jpg"></div>       

输入：root = [1,2,3], targetSum = 5           
输出：false       

示例 3：        
输入：root = [1,2], targetSum = 0       
输出：false        
 
提示：        
* 树中节点的数目在范围 [0, 5000] 内        
* -1000 <= Node.val <= 1000        
* -1000 <= targetSum <= 1000

#### Thought & AC         
深搜，没什么说的。      

```c++       
class Solution {
public:
    bool hasPathSum(TreeNode* root, int targetSum) {
        if (!root) return false;
        bool flag = false;
        find(root, flag, targetSum - root->val);
        return flag;
    }
private:
    void find(TreeNode* node, bool& flag, int target){
        if (!node->left && !node -> right){
            if (!(target)) flag = true;
            return;
        }
        if (node -> left) 
            find(node -> left, flag, target - node -> left -> val);
        if (node -> right) 
            find(node -> right, flag, target - node-> right -> val);
    }
};
```     

## 第 13 天 树           

### 700. 二叉搜索树中的搜索

给定二叉搜索树（BST）的根节点和一个值。 你需要在BST中找到节点值等于给定值的节点。 返回以该节点为根的子树。 如果节点不存在，则返回 NULL。       
例如，        
给定二叉搜索树:          
```
        4
       / \
      2   7
     / \
    1   3
```
和值: 2        
你应该返回如下子树:         
```
      2     
     / \   
    1   3
```
在上述示例中，如果要找的值是 5，但因为没有节点值为 5，我们应该返回 NULL。       

#### Thought & AC        
深搜，没什么好说的。      
```c++      
class Solution {
public:
    TreeNode* searchBST(TreeNode* root, int val) {
        if (!root || root -> val == val) return root;
        if (val < root -> val) return searchBST(root -> left, val);
        else return searchBST(root -> right, val);
    }
};
```

### 701. 二叉搜索树中的插入操作        
给定二叉搜索树（BST）的根节点和要插入树中的值，将值插入二叉搜索树。 返回插入后二叉搜索树的根节点。 输入数据 保证 ，新值和原始二叉搜索树中的任意节点值都不同。          
注意，可能存在多种有效的插入方式，只要树在插入后仍保持为二叉搜索树即可。 你可以返回 任意有效的结果 。         

示例 1：        
输入：root = [4,2,7,1,3], val = 5          
输出：[4,2,7,1,3,5]        
解释：另一个满足题目要求可以通过的树是：             

示例 2：         
输入：root = [40,20,60,10,30,50,70], val = 25       
输出：[40,20,60,10,30,50,70,null,null,25]        

示例 3：          
输入：root = [4,2,7,1,3,null,null,null,null,null,null], val = 5        
输出：[4,2,7,1,3,5]       

提示：          
* 给定的树上的节点数介于 0 和 10^4 之间       
* 每个节点都有一个唯一整数值，取值范围从 0 到 10^8          
* -10^8 <= val <= 10^8        
* 新值和原始二叉搜索树中的任意节点值都不同       

#### Thought & AC         
由于没有要求二叉树平衡，深搜将节点加入到最低层作为叶节点。           
```c++       
class Solution {
public:
    TreeNode* insertIntoBST(TreeNode* root, int val) {
        if (!root) return new TreeNode(val);
        if (val < root-> val){
            if (!root -> left) root -> left = new TreeNode(val);
            else insertIntoBST(root -> left, val);
        }
        else{
            if (!root -> right) root -> right = new TreeNode(val);
            else insertIntoBST(root -> right, val);
        }
        return root;
    }
};
```