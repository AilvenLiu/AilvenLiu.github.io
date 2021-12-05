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