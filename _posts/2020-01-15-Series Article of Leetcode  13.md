---
layout:     post
title:      Series Article of Leetcode Notes -- 15
subtitle:   Study Plan DS II 15 - 18 树      
date:       2021-11-30
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - leetcode      
    - c++      
    - data structure      
---     

> leetcode 刷题笔记，Study Plan Data Structure 2, Day 15 - 18 ： 树 。     

## 第 15 天 树            
### 108. 将有序数组转换为二叉搜索树             

给你一个整数数组 nums ，其中元素已经按 升序 排列，请你将其转换为一棵 高度平衡 二叉搜索树。
高度平衡 二叉树是一棵满足「每个节点的左右两个子树的高度差的绝对值不超过 1 」的二叉树。         

示例 1：         
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/leetcode/leetcode108.jpg"></div>       

输入：nums = [-10,-3,0,5,9]          
输出：[0,-3,9,-10,null,5]           
解释：[0,-10,5,null,-3,null,9] 也将被视为正确答案：            

示例 2：      
输入：nums = [1,3]          
输出：[3,1]        
解释：[1,3] 和 [3,1] 都是高度平衡二叉搜索树。         
  

提示：        
* 1 <= nums.length <= 104      
* -104 <= nums[i] <= 104       
* nums 按 严格递增 顺序排列          

#### Thought & AC        
二分法遍历数组+递归构造二叉树。通过一个 bool 变量决定构造左子树还是右子树。         
```c++         
class Solution {
public:
    TreeNode* sortedArrayToBST(vector<int>& nums) {
        int len = nums.size();
        int mid = len/2;
        TreeNode* root = new TreeNode(nums[mid]);
        if (mid-1>=0) treeBuild(root, nums, 0, mid-1, 0);
        if (nums.size()-1>= mid+1) treeBuild(root, nums, mid+1, nums.size()-1, 1);
        return root;
    }
private:
    void treeBuild(TreeNode* node, vector<int>& nums, int left, int right, bool flag){
        int mid = (right+left)/2;
        TreeNode* child = new TreeNode(nums[mid]);
        if (!flag) node -> left = child;
        else node -> right = child;
        if (mid-1 >= left) treeBuild(child, nums, left, mid-1, 0);
        if (right >= mid+1) treeBuild(child, nums, mid+1, right, 1);
    }
};
```

### 105. 从前序与中序遍历序列构造二叉树          
给定一棵树的前序遍历 preorder 与中序遍历  inorder。请构造二叉树并返回其根节点。          

示例 1:        
Input: preorder = [3,9,20,15,7], inorder = [9,3,15,20,7]            
Output: [3,9,20,null,null,15,7]            
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/leetcode/leetcode108.jpg"></div>        

示例 2:        
Input: preorder = [-1], inorder = [-1]            
Output: [-1]           

提示:         
* 1 <= preorder.length <= 3000            
* inorder.length == preorder.length           
* -3000 <= preorder[i], inorder[i] <= 3000            
* preorder 和 inorder 均无重复元素           
* inorder 均出现在 preorder          
* preorder 保证为二叉树的前序遍历序列           
* inorder 保证为二叉树的中序遍历序列         

#### Thought            
用递归去做，建树很简单，难点是怎么进行递归；设计递归的困难又在于，对前序遍历和中序遍历的结构的理解。便于理解的一种解释如下：           
前序遍历结构：[根节点，[左子树节点]，[右子树节点]]            
中序遍历结构：[[左子树节点]，根节点，[右子树节点]]          
而显然，对两种遍历结构，其 [子树节点] 中的节点数量和内容，是一样的。也就是说，中序和前序各自的相同侧子树节点数组单独拿出来后，就是当前节点子树的中序和前序遍历。这为递归创造了条件。           
于是，根据 前序遍历确定好根节点之后，再由中序即可以确定左右子树的范围（节点数量），从而就可以确定中序和前序左右子树的起始和终止 idx。         
传入参数：前序，中序，前序起终点，中序起终点。像正常的构造二叉树的流程一样，递归建树就可以了。            

#### AC Version            
```c++
class Solution {
public:
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        return buildTree(preorder, inorder, 0, preorder.size()-1, 0, inorder.size()-1);
    }
private:
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder, int lpre, int hpre, int lin, int hin){
        TreeNode* node = new TreeNode(preorder[lpre]);
        int mid = lin;
        while(mid<hin && inorder[mid] != preorder[lpre]) ++mid;
        int left = mid - lin;
        if (mid > lin) node -> left = buildTree(preorder, inorder, lpre+1, lpre+left, lin, mid-1);
        if (mid < hin) node -> right= buildTree(preorder, inorder, lpre+1+left, hpre, mid+1, hin);
        return node;
    }
};
```


### 103. 二叉树的锯齿形层序遍历           
给定一个二叉树，返回其节点值的锯齿形层序遍历。（即先从左往右，再从右往左进行下一层遍历，以此类推，层与层之间交替进行）。         
例如：           
给定二叉树 [3,9,20,null,null,15,7],            
```
    3
   / \
  9  20
    /  \
   15   7
```          
返回锯齿形层序遍历如下：            
```
[
  [3],
  [20,9],
  [15,7]
]
```        

#### Thought & AC Version            
这题思路很明晰：用队列进行广搜，每次 while 都使用一个 for 循环将当前层一次性（在同一次 while 内）处理完，从而实现整层同步输出。下层元素的加入就正常在队列从前往后、先左再右遍历，这样每层加入队列的新元素都是从左到右有序的。同时维护一个一维数组 row 从左向右顺序地存储当前层元素。         
使用一个 一维数组 存储当前层元素，使用一个 bool 变量记录当前是奇数层还是偶数层。如果是奇数层，从前往后顺序遍历 row 将元素 emplace_back 到结果二维数组的子数组中；如果 false，倒序 emplace_back。从而实现锯齿形遍历。               
```c++       
class Solution {
public:
    vector<vector<int>> zigzagLevelOrder(TreeNode* root) {
        vector<vector<int>> res;
        if (!root) return res;

        queue<TreeNode*> q;
        q.push(root);
        vector<int> row;
        row.emplace_back(root -> val);
        int count = 1;
        bool flag = true;
        while(!q.empty()){
            vector<int> tmp;
            int count_ = count;
            count = 0;
            if (flag){
                for (int i = 0; i < count_; i++)
                    tmp.emplace_back(row[i]);
                row.clear();
                for (int i = 0; i < count_; i++){
                    TreeNode* node = q.front();
                    q.pop();
                    if (node -> left){
                        row.emplace_back(node -> left -> val); 
                        q.push(node -> left);
                        count ++;
                    }
                    if (node -> right){ 
                        row.emplace_back(node -> right -> val);
                        q.push(node -> right);
                        count ++;
                    }
                }
                res.emplace_back(tmp);
                flag = false;
            }
            else{
                for (int i = count_-1; i>=0; --i)
                    tmp.emplace_back(row[i]);
                row.clear();
                for (int i = 0; i < count_; i++){
                    TreeNode* node = q.front();
                    q.pop();
                    if (node -> left){ 
                        row.emplace_back(node -> left -> val);
                        q.push(node -> left);
                        count ++;
                    }
                    if (node -> right){ 
                        row.emplace_back(node -> right -> val);
                        q.push(node -> right);
                        count ++;
                    }
                }
                res.emplace_back(tmp);
                flag = true;
            }
        }
        return res;
    }
};
```          

##  第 16 天 树         
### 199. 二叉树的右视图          
给定一个二叉树的 根节点 root，想象自己站在它的右侧，按照从顶部到底部的顺序，返回从右侧所能看到的节点值。          

示例 1:
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/leetcode/leetcode199.jpg"></div>        

输入: [1,2,3,null,5,null,4]          
输出: [1,3,4]          

示例 2:        
输入: [1,null,3]          
输出: [1,3]         

示例 3:        
输入: []         
输出: []       
 
提示:         
* 二叉树的节点个数的范围是 [0,100]            
* -100 <= Node.val <= 100         

#### Thought & AC Version        
用队列进行广搜，while 里面加一层 for 循环一次性处理完一整层节点。先右后左访问节点，进入 for 循环之前的当前队列 front() 元素就是当前层的最右侧节点，emplace_back() 进结果数组即可。         
```c++         
class Solution {
public:
    vector<int> rightSideView(TreeNode* root) {
        if (!root) return {};
        vector<int> res;
        queue<TreeNode*> q;
        q.push(root);
        int count = 1;
        while(!q.empty()){
            res.emplace_back(q.front() -> val);
            int count_ = count;
            count = 0;
            for (int i = 0; i < count_; i++){
                TreeNode* node = q.front();
                q.pop();
                if (node -> right){
                    q.push(node -> right);
                    ++count;
                }
                if (node -> left){
                    q.push(node -> left);
                    ++count;
                }
            }
        }
        return res;
    }
};
```

### 113. 路径总和 II           
给你二叉树的根节点 root 和一个整数目标和 targetSum ，找出所有 从根节点到叶子节点 路径总和等于给定目标和的路径。          
叶子节点 是指没有子节点的节点。          

示例 1：        
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/leetcode/leetcode199.jpg"></div>        

输入：root = [5,4,8,11,null,13,4,7,2,null,null,5,1], targetSum = 22       
输出：[[5,4,11,2],[5,8,4,5]]         

示例 2：      
输入：root = [1,2,3], targetSum = 5         
输出：[]           

示例 3：       
输入：root = [1,2], targetSum = 0           
输出：[]           

提示：        
* 树中节点总数在范围 [0, 5000] 内           
* -1000 <= Node.val <= 1000           
* -1000 <= targetSum <= 1000         

#### Thought & AC         
深搜 + 回溯。递归调用深搜函数，递归函数中先将当前节点 emplace_back 进入路径数组，分两种情况讨论：         
1. 到当前节点的路径和已经等于 target，且当前节点是叶节点加入路径数组到结果集。注意，不要讨论到当前节点路径总和已经等于 target 而当前节点非叶节点的情况，由于自当前节点下一节点至叶节点的子路径和可能为零。           
2. 否则，递归调用。递归调用完记得将路径数组中最后一个元素（也就是当前一轮调用中加入的元素）pop 出来，为同一层其他元素加入路径数组腾出位置。即回溯思想。       

```c++         
class Solution {
public:
    vector<vector<int>> pathSum(TreeNode* root, int targetSum) {
        vector<vector<int>> res;
        if (!root) return res;
        vector<int> path;
        dfs(root, targetSum, res, path);
        return res;
    }
private:
    void dfs(TreeNode* node, int targetSum, vector<vector<int>>& res, vector<int>& path){
        path.emplace_back(node -> val);
        int left = targetSum - node -> val;
        if (left == 0 && !node->left && !node->right) {
            res.emplace_back(path);
        }
        if (node -> left){
            dfs(node -> left, left, res, path);
            path.pop_back();
        }if (node -> right){
            dfs(node -> right, left, res, path);
            path.pop_back();
        }
    }
};
```


### 450. 删除二叉搜索树中的节点           
给定一个二叉搜索树的根节点 root 和一个值 key，删除二叉搜索树中的 key 对应的节点，并保证二叉搜索树的性质不变。返回二叉搜索树（有可能被更新）的根节点的引用。          

一般来说，删除节点可分为两个步骤：       
1. 首先找到需要删除的节点；         
2. 如果找到了，删除它。

示例 1:           
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/leetcode/leetcode450.jpg"></div>       

输入：root = [5,3,6,2,4,null,7], key = 3          
输出：[5,4,6,2,null,null,7]         
解释：给定需要删除的节点值是 3，所以我们首先找到 3 这个节点，然后删除它。       
一个正确的答案是 [5,4,6,2,null,null,7]。        
另一个正确答案是 [5,2,6,null,4,null,7]。            

示例 2:        
输入: root = [5,3,6,2,4,null,7], key = 0           
输出: [5,3,6,2,4,null,7]         
解释: 二叉树不包含值为 0 的节点         

示例 3:        
输入: root = [], key = 0         
输出: []           

提示:           
* 节点数的范围 [0, 104].           
* -105 <= Node.val <= 105          
* 节点值唯一                
* root 是合法的二叉搜索树           
* -105 <= key <= 105            

要求算法时间复杂度为 O(h)，h 为树的高度。           

#### Thought           
用递归。          
key 小于当前 node -> val，则 `node -> left = deleteNode(node -> left, key)；`      
key 大于当前 node -> val，则 `node ->right = deleteNode(node ->right, key)；`      
key 等于当前 node -> val，则要执行删除操作。也要分情况讨论：           
node 左右子树都不存在，返回 nullptr；         
只有一侧子树存在，返回该侧子树（相当于绕过了该节点，父节点直接连接到其子节点，也即 **从树中** 删除了该节点）。             
难点在于有双子树的节点的删除。需要：           
1. 左子树打包交付给新的 node 继续做左子树。          
2. 右子树中的最小值，也即从 node -> right 一直向左 while 循环找到最左的 left，我们记之为 tmp， 作为转移到新的 node 位置。       
3. tmp 必然没有左子树，其 left 直接指向原 node -> left 即可。         
4. tmp 有可能有右子树，还要分情况讨论：           
4.1. tmp 本身就是 node -> right，则不作任何改变。           
4.2. tmp 是 node -> right 的左子树中的节点。则需要将 tmp -> right （即使是 nullptr） 作为其父结点的 新的 left；而 tmp -> right 接收 node -> right。        
4.3. 为了判断 tmp 是否是 node -> right 本身，需要维护一个初始化为 nullptr 的 tmp2 跟随 tmp 一起更新，始终作为 tmp 的父节点；当 tmp 走到最左节点，若 tmp2 仍不存在，则 tmp == node -> right。          
5. 此时 node 的左右子树都已经被新的 tmp 接收，原来 tmp 的位置和子树也已妥善处置。可以使 node = nullptr，并返回 tmp 作为 node 父节点的新的子节点。         
   
画图示意如下：         
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/leetcode/leetcode450-1.jpg"></div>        


#### AC Version          
```c++       
class Solution {
public:
    TreeNode* deleteNode(TreeNode* root, int key) {
        if (!root) return nullptr;
        if (key < root->val) root -> left = deleteNode(root -> left, key);
        else if (key > root->val) root -> right = deleteNode(root -> right, key);
        else{
            if (!root -> left && !root -> right) return nullptr;
            if (!root -> left &&  root -> right) return root -> right;
            if ( root -> left && !root -> right) return root -> left;
            else{
                TreeNode* left = root -> left;
                TreeNode* tmp = root -> right;
                TreeNode* tmp2= nullptr;
                while(tmp->left) {
                    tmp2 = tmp;
                    tmp = tmp -> left;
                }
                tmp -> left = left;
                if (tmp2){
                    tmp2 -> left = tmp -> right; 
                    tmp -> right = root -> right;
                }
                root = nullptr;
                return tmp;
            }
        }
        return root;
    }
};
```    

## 第 17 天 树            
### 230. 二叉搜索树中第K小的元素
给定一个二叉搜索树的根节点 root ，和一个整数 k ，请你设计一个算法查找其中第 k 个最小元素（从 1 开始计数）。            

示例 1：
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/leetcode/leetcode230.jpg"></div>        

输入：root = [3,1,4,null,2], k = 1           
输出：1           

示例 2：         
输入：root = [5,3,6,2,4,null,null,1], k = 3          
输出：3          

提示：          
* 树中的节点数为 n 。          
* 1 <= k <= n <= 104         
* 0 <= Node.val <= 104           

#### Thought & AC          
二叉搜索树的中序遍历是升序数组，于是进行中序遍历。       
维护一个 vec 按照中序遍历的顺序升序存储节点值，当 vec.size() == k ，vec.back() 就是第 k 小的数。于是在递归函数中需要设计返回条件为 vec.size()>=k ，也即无论递归进行到哪一层，我始终按照 vec 的长度判断是否要继续向 vec 添加元素。              
```c++         
class Solution {
public:
    int kthSmallest(TreeNode* root, int k) {
        vector<int> vec;
        preorderTravel(root, vec, k);
        return vec.back();
    }
private:
    void preorderTravel(TreeNode* node, vector<int>& vec, int k){
        if(node -> left) preorderTravel(node -> left, vec, k);
        if (vec.size() >= k) return;
        vec.emplace_back(node -> val);
        if (node -> right) preorderTravel(node -> right, vec, k);
    }
};
```          

### 173. 二叉搜索树迭代器           
实现一个二叉搜索树迭代器类BSTIterator ，表示一个按中序遍历二叉搜索树（BST）的迭代器：           
* `BSTIterator(TreeNode root)` 初始化 BSTIterator 类的一个对象。BST 的根节点 root 会作为构造函数的一部分给出。指针应初始化为一个不存在于 BST 中的数字，且该数字小于 BST 中的任何元素。               
* `boolean hasNext()` 如果向指针右侧遍历存在数字，则返回 true ；否则返回 false 。              
* `int next()`将指针向右移动，然后返回指针处的数字。             
注意，指针初始化为一个不存在于 BST 中的数字，所以对 next() 的首次调用将返回 BST 中的最小元素。             

你可以假设 next() 调用总是有效的，也就是说，当调用 next() 时，BST 的中序遍历中至少存在一个下一个数字。           

示例：           
```
输入               
["BSTIterator", "next", "next", "hasNext", "next", "hasNext", "next", "hasNext", "next", "hasNext"]
[[[7, 3, 15, null, null, 9, 20]], [], [], [], [], [], [], [], [], []]

输出
[null, 3, 7, true, 9, true, 15, true, 20, false]

解释
BSTIterator bSTIterator = new BSTIterator([7, 3, 15, null, null, 9, 20]);
bSTIterator.next();    // 返回 3
bSTIterator.next();    // 返回 7
bSTIterator.hasNext(); // 返回 True
bSTIterator.next();    // 返回 9
bSTIterator.hasNext(); // 返回 True
bSTIterator.next();    // 返回 15
bSTIterator.hasNext(); // 返回 True
bSTIterator.next();    // 返回 20
bSTIterator.hasNext(); // 返回 False
```

提示：         
* 树中节点的数目在范围 [1, 105] 内           
* 0 <= Node.val <= 106           
* 最多调用 105 次 hasNext 和 next 操作          

进阶：             
你可以设计一个满足下述条件的解决方案吗？next() 和 hasNext() 操作均摊时间复杂度为 O(1) ，并使用 O(h) 内存。其中 h 是树的高度。              

#### Thought         
需要变通而灵活地理解中序遍历是 “左子树 --> 当前节点 --> 右子树” 的顺序。此处直接给出思路：          
1. 维护一个节点栈 treeStack 该栈按顺序存储中序遍历的节点。        
2. 构造函数。应当从 root 节点开始一直寻找左子树节点，并按顺序 push 进 treeStack。直到 节点走到最左左子树的 left，也就是 nullptr。        
3. next 函数。应当返回并 pop 出栈顶元素，同时压入新节点。从栈顶元素的右子树节点开始，重新走一遍构造函数的压栈流程：一直向左找左子树节点，并按顺序 push 进栈，直到左子树节点为空。        
4. hasNext 函数。返回栈内是否还有元素的判断。        

#### AC Version          
```c++
class BSTIterator {
private:
    stack<TreeNode*> treeStack;
public:
    BSTIterator(TreeNode* root){
        while(root){
            treeStack.push(root);
            root = root -> left;
        }
    }
    
    int next() {
        TreeNode* node = treeStack.top();
        treeStack.pop();
        if (node -> right){
            TreeNode* nodeRight = node -> right;
            while(nodeRight){
                treeStack.push(nodeRight);
                nodeRight = nodeRight -> left;
            }
        }
        return node -> val;
    }
    
    bool hasNext() {
        return !treeStack.empty();
    }
};

```       

## 第 18 天 树             

### 236. 二叉树的最近公共祖先           
给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。          
最近公共祖先的定义为：“对于有根树 T 的两个节点 p、q，最近公共祖先表示为一个节点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”         

示例 1：
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/leetcode/leetcode236.png"></div>        

输入：root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 1          
输出：3           
解释：节点 5 和节点 1 的最近公共祖先是节点 3 。          

示例 2：           
输入：root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 4           
输出：5           
解释：节点 5 和节点 4 的最近公共祖先是节点 5 。因为根据定义最近公共祖先节点可以为节点本身。           

示例 3：          
输入：root = [1,2], p = 1, q = 2           
输出：1         
  

提示：           
* 树中节点数目在范围 [2, 105] 内。           
* -109 <= Node.val <= 109             
* 所有 Node.val 互不相同 。              
* p != q              
* p 和 q 均存在于给定的二叉树中。

#### Thought          
直接给出思路：         
维护一个节点栈，初始化压入 root 到节点 p 的路径。现在从节点栈将节点元素依次出栈，其深度也就依次从深到浅。用 while 每出栈一个元素，从该元素开始寻找是否能找到节点 q ，若是，则压栈函数结束后直接返回该节点即可。为减少代码量，应当复用寻找节点 p 路径时的压栈函数：每轮 while 新建一个 tmp 节点栈即可。           
由于是寻找路径，压栈函数显然应当递归+回溯。则比较难想到的是压栈函数的退出条件。      
给出一个 bool 值 flag，初始为 false：      
1. 若 flag == true，直接退出。            
2. 若 1 不成立，将当前元素压栈。随后判断栈顶元素是否为 q ，若是，flag = true，退出函数。          
3. 若上一步没有退出，说明仍没有找到元素 q 。则开始递归调用：           
   若当前元素左子树存在，调用压栈函数，从左子树元素开始寻找；        
   若当前元素右子树存在，调用压栈函数，从右子树元素开始寻找；        
   **！！但是注意回溯和回溯条件！！**：          
   当本次递归调用结束，***当且仅当！***， flag 仍为 false，说明仍然没有找到 q，在且只在这种情况下，需要将当前栈顶元素 pop 出去，为同层其他元素的进入腾出位置。如果 调用结束 flag 为 true 了，说明已经找到 q，此时栈元素就不要动了。       

#### AC Version           
```c++
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        stack<TreeNode*> path;
        bool flag = false;
        findPath(root, p, path, flag);
        TreeNode* commonAncester;
        flag = false;
        
        while(!path.empty()){
            stack<TreeNode*> tmp;
            commonAncester = path.top();
            // printf("this: %d\n", commonAncester->val);
            path.pop();
            findPath(commonAncester, q, tmp, flag);
            if (flag) return commonAncester;
        }
        
        return nullptr;
    }
private:
    void findPath(TreeNode* node, TreeNode* p, stack<TreeNode*>& path, bool& flag){
        if (flag) return;
        path.push(node);
        if (path.top() == p) {
            flag = true; 
            return;
        }
        if (node -> left){
            findPath(node -> left, p, path, flag);
            if (!flag) path.pop();
        }
        if (node -> right){
            findPath(node -> right, p, path, flag);
            if (!flag) path.pop();
        }
    }
};
```        

### 297. 二叉树的序列化与反序列化
序列化是将一个数据结构或者对象转换为连续的比特位的操作，进而可以将转换后的数据存储在一个文件或者内存中，同时也可以通过网络传输到另一个计算机环境，采取相反方式重构得到原数据。                
请设计一个算法来实现二叉树的序列化与反序列化。这里不限定你的序列 / 反序列化算法执行逻辑，你只需要保证一个二叉树可以被序列化为一个字符串并且将这个字符串反序列化为原始的树结构。          

示例 1：       
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/leetcode/leetcode236.png"></div>        

输入：root = [1,2,3,null,null,4,5]          
输出：[1,2,3,null,null,4,5]              

示例 2：         
输入：root = []           
输出：[]           

示例 3：      
输入：root = [1]           
输出：[1]          

示例 4：         
输入：root = [1,2]          
输出：[1,2]         
 
提示：         
* 树中结点数在范围 [0, 104] 内           
* -1000 <= Node.val <= 1000          

#### Thought            
本题关键在于 stringstream 的使用，结合二叉树递归遍历和建树，很容易可以做出。     
1. 序列化。用递归去做，从 root 开始，按照前序遍历的顺序，返回字符串表示的当前节点值和左右子树的递归调用，再返回之前要进行一次判断：当前节点是否存在，如不存在，返回一个代表 nullptr 的特定值，我们用 # 表示。由于 stringstream 默认按照空格分隔字符串，节点之间（也就是当前节点和递归调用左右子树之间）用空格隔开。       
注意，一定要前序。这主要是受限于反序列化，也就是从序列化数据中重建出一棵树的时候，需要先有当前节点，才能从当前节点建立左子树和右子树。             
2. 反序列化。声明一个 stringstream 对象，同时赋值为参数传入的 string ，也就是序列化后的字符串数据。调用以该 stringstream 对象为参数的 build_tree 函数，将返回二叉树根节点。       
3. 二叉树建树。声明一个 string p 代表当前节点值，由于 stringstream 对象通过空格分隔字符串，而在序列化函数中我们已经将二叉树按照前序遍历的顺序以空格进行了分割，此时使用 `>>` 右移运算符可以讲 stringstream 第一个空格前的子串 **转移** 给 p。然后根据 p 的值考虑下一步即可：        
   * p == "#"：意味着当前节点是 nullptr，则直接返回 nullptr。         
   * 否则，建立一个值为 p 的节点 node。string -> int 用库函数 stoi(p) 。           
   * 按照先左后右的顺序递归调用 build_tree(ss)。        
返回当前建立的节点 node 。        

#### AC Version             
```c++           
class Codec {
public:
    string serialize(TreeNode* root) {
        if (!root) return "#";
        return to_string(root->val) + " " + serialize(root -> left) + " " + serialize(root -> right);
    }

    TreeNode* deserialize(string data) {
        stringstream ss(data);
        return build_tree(ss);
    }

    TreeNode* build_tree(stringstream& ss){
        string p;
        ss >> p;
        if (p == "#") return nullptr;
        TreeNode* root = new TreeNode(stoi(p));
        root -> left = build_tree(ss);
        root -> right = build_tree(ss);
        return root;
    }
};
```