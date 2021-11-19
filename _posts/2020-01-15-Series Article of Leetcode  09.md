---
layout:     post
title:      Series Article of Leetcode Notes -- 09
subtitle:   Study Plan DS II 01-07      
date:       2021-11-16
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - leetcode      
    - c++      
    - data structure      
---     

> leetcode 刷题笔记，Study Plan Data Structure 2, Day 1 -- Day 7 。         

## 第 1 天 数组           

### 136. 只出现一次的数字
给定一个非空整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。        

说明：         
你的算法应该具有线性时间复杂度。 你可以不使用额外空间来实现吗？          

示例 1:       
输入: [2,2,1]       
输出: 1           
示例 2:         

输入: [4,1,2,1,2]         
输出: 4          

#### Thought & AC          
排好序后从零开始遍历（idx 始于 0 的偶数位），如果某个元素和其后面一位相等，则额外进行一次 i++，也就是总共向后走两位移到下一个偶数位；直到不相等，返回该值。       
```c++        
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        if (nums.size() == 1) return nums.at(0);
        sort(nums.begin(), nums.end());
        for (int i = 0; i < nums.size()-1; i++){
            if (nums.at(i) == nums.at(i+1))
                i+=1;
            else return nums.at(i);
        }
        return nums[nums.size()-1];
    }
};
```        
执行用时： 24 ms, 在所有 C++ 提交中击败了 23.82% 的用户          
内存消耗： 16.4 MB, 在所有 C++ 提交中击败了 77.16% 的用户       

### 169. 多数元素     
给定一个大小为 n 的数组，找到其中的多数元素。多数元素是指在数组中出现次数 大于 ⌊ n/2 ⌋ 的元素。      
你可以假设数组是非空的，并且给定的数组总是存在多数元素。       

示例 1：       
输入：[3,2,3]      
输出：3      
示例 2：      

输入：[2,2,1,1,1,2,2]      
输出：2      

进阶：         
尝试设计时间复杂度为 O(n)、空间复杂度为 O(1) 的算法解决此问题。        

#### Thought & AC           
则排好序后的数组的 idx=[n/2] 处必是多数元素。          
```c++         
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        return nums.at(nums.size()/2);
    }
};
```        
执行用时： 16 ms, 在所有 C++ 提交中击败了 73.03% 的用户      
内存消耗： 19.2 MB, 在所有 C++ 提交中击败了 20.66% 的用户        

### 15. 三数之和        
给你一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？请你找出所有和为 0 且不重复的三元组。        
注意：答案中不可以包含重复的三元组。      

示例 1：       
输入：nums = [-1,0,1,2,-1,-4]         
输出：[[-1,-1,2],[-1,0,1]]    

示例 2：       
输入：nums = []       
输出：[]       

示例 3：       
输入：nums = [0]       
输出：[]       
 
提示：        
* 0 <= nums.length <= 3000        
* -105 <= nums[i] <= 105

#### Thought         
本质上就是一个两数和：遍历 idx = 0 ~ nums.size()-3， 以 -nums[idx] 作为 target 查找两数和。则可以通过有序数组的 while(left < right) 高效查找。       
需要注意的是本题存在重复元素，则查找过程需要避开：如果当前元素和查找过的上一元素值相等，continue  或 while 走到当前行走方向第一个不相等元素；当然，while 的时候需要限定 idx 范围，否则会遇到 index out of range 错误。           

#### My AC Version         
```c++    
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> res;
        if (nums.size() < 3) return res;
        sort(nums.begin(), nums.end());
        for (int i = 0; i < nums.size()-2; i++){
            if (i != 0 && nums.at(i) == nums.at(i-1)) continue;
            twoSum(nums, res, i, -nums.at(i));
        }
        return res;
    }
private:
    void twoSum(vector<int>& nums, vector<vector<int>>& res, int begin, int target){
        int i = begin+1, j = nums.size()-1;
        while(i<j){
            int tmp = nums.at(i) + nums.at(j);
            if (tmp == target){
                res.emplace_back(vector<int> {nums.at(begin), nums.at(i), nums.at(j)});
                i++; while(i<nums.size() && nums.at(i) == nums.at(i-1)) i++;
                j--; while(j>=0 && nums.at(j) == nums.at(j+1)) j--;
            }
            else if (tmp < target) i++;
            else if (tmp > target) j--;
        }
    }
};
```  

## 第 2 天 数组           
### 75. 颜色分类         
给定一个包含红色、白色和蓝色，一共 n 个元素的数组，原地对它们进行排序，使得相同颜色的元素相邻，并按照红色、白色、蓝色顺序排列。      
此题中，我们使用整数 0、 1 和 2 分别表示红色、白色和蓝色。        

示例 1：      
输入：nums = [2,0,2,1,1,0]         
输出：[0,0,1,1,2,2]        

示例 2：       
输入：nums = [2,0,1]       
输出：[0,1,2]      

示例 3：       
输入：nums = [0]        
输出：[0]        

示例 4：       
输入：nums = [1]        
输出：[1]      

提示：       
* n == nums.length      
* 1 <= n <= 300      
* nums[i] 为 0、1 或 2      
 
#### Thought & AC       
本质就是一个排序，要求原地而稳定，用冒泡。       
```c++       
class Solution {
public:
    void sortColors(vector<int>& nums) {
        int len = nums.size();
        for (int i = 0; i < len-1; i++){
            bool flag = false;
            for (int j = 0; j < len - 1 - i; j++){
                if (nums.at(j) > nums.at(j+1)){
                    swap(nums[j], nums[j+1]);
                    flag = true;
                }
            }
            if (!flag){break;}
        }
    }
};
```       

排序算法的总结见[]() 。            

### 56. 合并区间       
以数组 intervals 表示若干个区间的集合，其中单个区间为 intervals[i] = [starti, endi] 。请你合并所有重叠的区间，并返回一个不重叠的区间数组，该数组需恰好覆盖输入中的所有区间。       

示例 1：     
输入：intervals = [[1,3],[2,6],[8,10],[15,18]]       
输出：[[1,6],[8,10],[15,18]]        
解释：区间 [1,3] 和 [2,6] 重叠, 将它们合并为 [1,6].         

示例 2：      
输入：intervals = [[1,4],[4,5]]      
输出：[[1,5]]         
解释：区间 [1,4] 和 [4,5] 可被视为重叠区间。        
  

提示：         
* 1 <= intervals.length <= 104         
* intervals[i].length == 2      
* 0 <= starti <= endi <= 104        

#### Thought & AC         
也没什么意思，按照每个元素第一个值（默认）排好序后，遍历往后走就是了。如果后一个元素的 start 值 小于等于前一个元素的 end，就是重叠区块，则更新当前 res 的末元素；否则添加到 res 中。           

```c++          
class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        vector<vector<int>> res;
        sort(intervals.begin(),intervals.end());
        res.emplace_back(intervals.at(0));
        for (int i = 1; i < intervals.size(); i++){
            if (intervals[i][0] <= res.back()[1])
                res.back()[1] = max(intervals[i][1], res.back()[1]);
            else res.emplace_back(intervals[i]);
        }
        return res;
    }
};
```          

### 706. 设计哈希映射      

不使用任何内建的哈希表库设计一个哈希映射（HashMap）。       

实现 MyHashMap 类：         
* MyHashMap() 用空映射初始化对象         
* void put(int key, int value) 向 HashMap 插入一个键值对 (key, value) 。如果 key 已经存在于映射中，则更新其对应的值 value 。      
* int get(int key) 返回特定的 key 所映射的 value ；如果映射中不包含 key 的映射，返回 -1 。         
* void remove(key) 如果映射中存在 key 的映射，则移除 key 和它所对应的 value 。        
  

示例：       
输入：
["MyHashMap", "put", "put", "get", "get", "put", "get", "remove", "get"]         
[[], [1, 1], [2, 2], [1], [3], [2, 1], [2], [2], [2]]        
输出：        
[null, null, null, 1, -1, null, 1, null, -1]         

解释：       
```c++
MyHashMap myHashMap = new MyHashMap();   
myHashMap.put(1, 1); // myHashMap 现在为 [[1,1]]
myHashMap.put(2, 2); // myHashMap 现在为 [[1,1], [2,2]]
myHashMap.get(1);    // 返回 1 ，myHashMap 现在为 [[1,1], [2,2]]
myHashMap.get(3);    // 返回 -1（未找到），myHashMap 现在为 [[1,1], [2,2]]
myHashMap.put(2, 1); // myHashMap 现在为 [[1,1], [2,1]]（更新已有的值）
myHashMap.get(2);    // 返回 1 ，myHashMap 现在为 [[1,1], [2,1]]
myHashMap.remove(2); // 删除键为 2 的数据，myHashMap 现在为 [[1,1]]
myHashMap.get(2);    // 返回 -1（未找到），myHashMap 现在为 [[1,1]]
```          
提示：
* 0 <= key, value <= 106        
* 最多调用 104 次 put、get 和 remove 方法        

 
### 706. 设计哈希映射          
不使用任何内建的哈希表库设计一个哈希映射（HashMap）。        

实现 MyHashMap 类：            
* MyHashMap() 用空映射初始化对象         
* void put(int key, int value) 向 HashMap 插入一个键值对 (key, value) 。如果 key 已经存在于映射中，则更新其对应的值 value 。      
* int get(int key) 返回特定的 key 所映射的 value ；如果映射中不包含 key 的映射，返回 -1 。       
* void remove(key) 如果映射中存在 key 的映射，则移除 key 和它所对应的 value 。

示例：         
输入：      
["MyHashMap", "put", "put", "get", "get", "put", "get", "remove", "get"]        
[[], [1, 1], [2, 2], [1], [3], [2, 1], [2], [2], [2]]      
输出：
[null, null, null, 1, -1, null, 1, null, -1]       

解释：
```c++
MyHashMap myHashMap = new MyHashMap();
myHashMap.put(1, 1); // myHashMap 现在为 [[1,1]]
myHashMap.put(2, 2); // myHashMap 现在为 [[1,1], [2,2]]
myHashMap.get(1);    // 返回 1 ，myHashMap 现在为 [[1,1], [2,2]]
myHashMap.get(3);    // 返回 -1（未找到），myHashMap 现在为 [[1,1], [2,2]]
myHashMap.put(2, 1); // myHashMap 现在为 [[1,1], [2,1]]（更新已有的值）
myHashMap.get(2);    // 返回 1 ，myHashMap 现在为 [[1,1], [2,1]]
myHashMap.remove(2); // 删除键为 2 的数据，myHashMap 现在为 [[1,1]]
myHashMap.get(2);    // 返回 -1（未找到），myHashMap 现在为 [[1,1]]
```

提示：      
* 0 <= key, value <= 106      
* 最多调用 104 次 put、get 和 remove 方法       

#### Thought         
实现一个 hash 表，需要解决以下几个关键问题：          
1. 哈希函数：能够将集合中任意可能的元素映射到一个固定范围的整数值，并将该元素存储到整数值对应的地址上。       
2. 冲突处理：由于不同元素可能映射到相同的整数值，因此需要在整数值出现「冲突」时，需要进行冲突处理。总的来说，有以下几种策略解决冲突：         
   * 链地址法：为每个哈希值维护一个链表，并将具有相同哈希值的元素都放入这一链表当中。          
   * 开放地址法：当发现哈希值 h 处产生冲突时，根据某种策略，从 h 出发找到下一个不冲突的位置。例如，一种最简单的策略是，不断地检查 h+1,h+2,h+3,\ldotsh+1,h+2,h+3,… 这些整数对应的位置。      
   * 再哈希法：当发现哈希冲突后，使用另一个哈希函数产生一个新的地址。      

3. 扩容：当哈希表元素过多时，冲突的概率将越来越大，而在哈希表中查询一个元素的效率也会越来越低。因此，需要开辟一块更大的空间，来缓解哈希表中发生的冲突。      

使用链地址法实现：        
设哈希表的大小为 *base*，则可以设计一个简单的哈希函数： hash(*x*) = *x* mod *base* 。        

哈希表需要随机存取，也即可以通过下标访存hash 标的具体位置 —— 使用 vector 结构。我们开辟一个大小为 *base* 的数组，数组的每个位置是一个链表。当计算出哈希值之后，就插入到对应位置的链表当中。      
由于我们使用整数除法作为哈希函数，为了尽可能避免冲突，应当将 *base* 取为一个质数。在这里，我们取 *base=769* 。      

#### AC Version         
```c++     
class MyHashMap {
private:
    vector<list<pair<int, int>>> data = \
    vector<list<pair<int, int>>>(769); 
    int getHash(int key){
        return key % 769;
    }
public:

    MyHashMap() {}
    
    void put(int key, int value) {
        int lo = getHash(key);
        for (list<pair<int, int>>::iterator it = data[lo].begin(); 
        it != data[lo].end(); ++it){
            if (std::get<0>(*it) == key){
                it -> second = value;
                return;
            }
        }
        data[lo].emplace_back(make_pair(key, value));
    }
    
    int get(int key) {
        int lo = getHash(key);
        for (list<pair<int, int>>::iterator it = data[lo].begin();
        it != data[lo].end(); ++it){
            if (std::get<0>(*it) == key)
                return std::get<1>(*it);
        }
        return -1;
    }
    
    void remove(int key) {
        int lo = getHash(key);
        for (list<pair<int, int>>::iterator it = data[lo].begin();
        it != data[lo].end(); it++){
            if (std::get<0>(*it) == key){
                data[lo].remove(*it);
                return;
            }
        }
    }
};
```

      
## 第 3 天 数组          

### 119. 杨辉三角 II         
给定一个非负索引 rowIndex，返回「杨辉三角」的第 rowIndex 行。在「杨辉三角」中，每个数是它左上方和右上方的数的和。          

示例 1:       
输入: rowIndex = 3         
输出: [1,3,3,1]          

示例 2:       
输入: rowIndex = 0         
输出: [1]         

示例 3:       
输入: rowIndex = 1        
输出: [1,1]          
 
提示:         
* 0 <= rowIndex <= 33      

#### Thought & AC           
一个比较简单的动态规划，逐行更新。每行仅与它上一行内容有关，每向下一行，在末尾push_back 一个 1；从第三行开始，有内部元素（非首尾两端的元素）可以更新。      
```c++      
class Solution {
public:
    vector<int> getRow(int rowIndex) {
        vector<int> res;

        for (int i = 0; i <= rowIndex; ++i){
            res.emplace_back(1);
            if (i > 1){
                vector<int> tmp(res);
                for (int j = 1; j < i; j++)
                    res[j] = tmp[j-1] + tmp[j];
            }
        }
        return res;
    }
};
```      

### 48. 旋转图像          
给定一个 n × n 的二维矩阵 matrix 表示一个图像。请你将图像顺时针旋转 90 度。       
你必须在 原地 旋转图像，这意味着你需要直接修改输入的二维矩阵。请不要 使用另一个矩阵来旋转图像。       

示例 1：       
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/leetcode/leetcode048-1.jpg"></div>       

输入：matrix = [[1,2,3],[4,5,6],[7,8,9]]        
输出：[[7,4,1],[8,5,2],[9,6,3]]         

示例 2：           
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/leetcode/leetcode048-2.jpg"></div>       

输入：matrix = [[5,1,9,11],[2,4,8,10],[13,3,6,7],[15,14,12,16]]      
输出：[[15,13,2,5],[14,3,4,1],[12,6,8,9],[16,7,10,11]]         

示例 3 ：        
输入：matrix = [[1]]        
输出：[[1]]        

示例 4：       
输入：matrix = [[1,2],[3,4]]        
输出：[[3,1],[4,2]]      

提示：
* matrix.length == n       
* matrix[i].length == n      
* 1 <= n <= 20        
* -1000 <= matrix[i][j] <= 1000        

#### Thought       
偶然发现的：一个矩形顺时针旋转 90 度，等效于先沿着 左上-右下 对角线翻转，再沿着垂直中线翻转。        

#### AC Version      
```c++        
class Solution {
public:
    void rotate(vector<vector<int>>& matrix) {
        int n = matrix.size();
        if (n == 1) return;
        for (int i = 0; i < n; ++i){
            for (int j = 0; j < i; ++j)
                swap(matrix[i][j], matrix[j][i]);
        }
        for (int i = 0; i < n/2; i++){
            for (int j = 0; j < n; j++)
                swap(matrix[j][i], matrix[j][n-1-i]);
        }
    }
};
```          