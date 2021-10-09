---
layout:     post
title:      Series Article of Leetcode Notes -- 03
subtitle:   Study Plan Algo 2 Day 1-7      
date:       2021-10-06
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - leetcode      
    - c++     
    - algorithm            
---     

> leetcode 刷题笔记，Study Plan Algorithm 2, Day 1 -- Day 7 。      

## Day 1 Binary Search            
[GitHub 连接](https://github.com/OUCliuxiang/leetcode/blob/master/StudyPlan/Algo2/day01)          

### 34. Find First and Last Position of Element in Sorted Array           

Given an array of integers nums sorted in ascending order, find the starting and ending position of a given target value.          
If target is not found in the array, return [-1, -1].          
You must write an algorithm with O(log n) runtime complexity.           

Example 1:         
Input: nums = [5,7,7,8,8,10], target = 8             
Output: [3,4]        

Example 2:         
Input: nums = [5,7,7,8,8,10], target = 6            
Output: [-1,-1]           

Example 3:          
Input: nums = [], target = 0            
Output: [-1,-1]           

Constraints:
* 0 <= nums.length <= 105           
* -109 <= nums[i] <= 109            
* nums is a non-decreasing array.            
* -109 <= target <= 109

#### My AC Version           
```c++     
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        vector<int> res(2, -1);
        if (nums.size() == 0) return res;
        int left = 0, right = nums.size()-1, middle;
        while(left <= right){
            middle = left + (right-left)/2;
            if (target == nums[middle])
                break;
            if (target > nums[middle])
                left = middle + 1;
            else 
                right = middle - 1;
        }
        if (target != nums[middle])
            return res;
        left = middle;  right = middle;
        while(left >= 0 && nums[left] == target)
            left --;
        res[0] = left + 1;
        while(right < nums.size() && nums[right] == target)
            right ++;
        res[1] = right - 1;
        return res;
    }
};
```       
Runtime: 4 ms, faster than **95.68%** of C++ online submissions for Find First and Last Position of Element in Sorted Array.
Memory Usage: 13.6 MB, less than 85.13% of C++ online submissions for Find First and Last Position of Element in Sorted Array.       

比较中庸的二分搜索，空间表现偶尔（根据submit而随机）不太好。            


#### Discuss solution          

讨论区比较出众，也比较符合我的预期的解法是使用 `lower_bound` / `upper_bound` 库方法一步求解：         
```c++     
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        int lo = lower_bound(nums.begin(), nums.end(), target) 
            - nums.begin();
        if (lo == nums.size() || nums[lo] != target)
            return {-1, -1};
        int hi = upper_bound(nums.begin(), nums.end(), target) 
            - nums.begin();
        return {lo, hi-1};
    }
};
```         
Runtime: 8 ms, faster than 69.85% of C++ online submissions for Find First and Last Position of Element in Sorted Array.         
Memory Usage: 13.7 MB, less than 17.64% of C++ online submissions for Find First and Last Position of Element in Sorted Array.           
属于前任造轮子后人乘凉，但是这个轮子的时间空间表现，一言难尽。         

### 33. Search in Rotated Sorted Array         
There is an integer array nums sorted in ascending order (with distinct values).              
Prior to being passed to your function, nums is possibly rotated at an unknown pivot index k (1 <= k < nums.length) such that the resulting array is [nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]] (0-indexed). For example, [0,1,2,4,5,6,7] might be rotated at pivot index 3 and become [4,5,6,7,0,1,2].            
Given the array nums after the possible rotation and an integer target, return the index of target if it is in nums, or -1 if it is not in nums.        
You must write an algorithm with O(log n) runtime complexity.

Example 1:          
Input: nums = [4,5,6,7,0,1,2], target = 0           
Output: 4           

Example 2:          
Input: nums = [4,5,6,7,0,1,2], target = 3             
Output: -1            

Example 3:            
Input: nums = [1], target = 0           
Output: -1            

Constraints:                
* 1 <= nums.length <= 5000            
* -104 <= nums[i] <= 104       
* All values of nums are unique.           
* nums is an ascending array that is possibly rotated.        
* -104 <= target <= 104

#### My AC Version         
```c++       
class Solution {
public:
    int search(vector<int>& nums, int target) {
        vector<int>::iterator it = find(nums.begin(), nums.end(), target);
        return it == nums.end() ? -1 : it - nums.begin();
    }
};
```        
Runtime: 8 ms, faster than 20.56% of C++ online submissions for Search in Rotated Sorted Array.       
Memory Usage: 11.1 MB, less than 75.41% of C++ online submissions for Search in Rotated Sorted Array.       
按照非排序区间进行调用线性查找库函数 find(begin, end, tar)，可得答案。时间空间表现差了些。          

也可以使用库函数 min_element() 寻找出最小元素，然后将偏移后的有序数组重新排序为真·有序数组，从而使用适用于有序数组的有 O(logn) 复杂度的 binary_search 和 lower_bound() ：           
```c++ 
class Solution {
public:
    int search(vector<int>& nums, int target) {
        vector<int>::iterator it = min_element(nums.begin(), nums.end());
        int shift = it - nums.begin();
        for (int i = 0; i < shift; i++){
            nums.emplace_back(nums[0]);
            nums.erase(nums.begin());
        }
        nums.erase(max_element(nums.begin(), nums.end())+1, nums.end());
        if (!binary_search(nums.begin(), nums.end(), target))
            return -1;
        
        return (lower_bound(nums.begin(), nums.end(), target) - nums.begin() + shift)%nums.size();
    }
};
```         
Runtime: 8 ms, faster than 20.56% of C++ online submissions for Search in Rotated Sorted Array.          
Memory Usage: 11.1 MB, less than 29.52% of C++ online submissions for Search in Rotated Sorted Array.      
时间空间表现也不怎么样。还是继续看讨论区。           

#### Discuss solution       
```c++           
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int left = 0, right = nums.size()-1, middle;
        while(left <= right){
            middle = (left + right)/2;
            if (nums[middle] == target)
                return middle;
            if (nums[left] <= nums[middle]){
                if (target >= nums[left] && target < nums[middle])
                    right = middle-1;
                else
                    left = middle + 1;
            }
            else{
                if (target > nums[middle] && target <= nums[right])
                    left = middle + 1;
                else
                    right = middle - 1;
            }
        }
        return -1;
    }
};
```          
Runtime: 4 ms, faster than **72.45%** of C++ online submissions for Search in Rotated Sorted Array.         
Memory Usage: 11.2 MB, less than 29.52% of C++ online submissions for Search in Rotated Sorted Array.      
代码比较好理解，纯手工实现了在有偏移有序数组中的二分搜索，时间表现有较大进步，空间表现还是不行，这样吧，留点遗憾。简单而言，就是在判断完 middle 左侧数组是否为有序后，加一步对 target 是否在该序列中的判断。       


### 74. Search a 2D Matrix      
Write an efficient algorithm that searches for a value in an m x n matrix. This matrix has the following properties:       
* Integers in each row are sorted from left to right.      
* The first integer of each row is greater than the last integer of the previous row.          

Example 1:          
Input: matrix = [[1,3,5,7],[10,11,16,20],[23,30,34,60]], target = 3        
Output: true           

Example 2:         
Input: matrix = [[1,3,5,7],[10,11,16,20],[23,30,34,60]], target = 13       
Output: false      

Constraints:         
* m == matrix.length           
* n == matrix[i].length       
* 1 <= m, n <= 100          
* -104 <= matrix[i][j], target <= 104

#### My AC Version       
```c++       
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        int n = matrix[0].size()-1;
        for(int i = 0; i < matrix.size(); i ++)
            if (target >= matrix[i].at(0) && target <= matrix[i].at(n))
                return binary_search(matrix[i].begin(), matrix[i].end(), target);
        return false;
    }
};
```        
Runtime: 0 ms, faster than **100.00%** of C++ online submissions for Search a 2D Matrix.        
Memory Usage: 9.5 MB, less than 79.06% of C++ online submissions for Search a 2D Matrix.            
调用库函数进行判断，时间空间表现还过得去。其实这个题完全不难。            

```c++      
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        int n = matrix[0].size()-1;
        for(int i = 0; i < matrix.size(); i ++)
            if (target >= matrix[i].at(0) && target <= matrix[i].at(n)){
                int left = 0, right = n, middle;
                while(left <= right){
                    middle = (left + right) / 2;
                    if (matrix[i][middle] == target)    return true;
                    if (matrix[i][middle] > target)
                        right = middle - 1;
                    else
                        left = middle + 1;
                }
                return false;
            }
        return false;
    }
};
```         
Runtime: 4 ms, faster than 74.80% of C++ online submissions for Search a 2D Matrix.       
Memory Usage: 9.3 MB, less than **99.98%** of C++ online submissions for Search a 2D Matrix.        

Runtime: 0 ms, faster than **100%** of C++ online submissions for Search a 2D Matrix.      
Memory Usage: 9.6 MB, less than 52.38% of C++ online submissions for Search a 2D Matrix.           

自己写二分的话，效率也不差。就不看讨论区了。         

## Day 2 Binary Search           
[GitHub 连接](https://github.com/OUCliuxiang/leetcode/blob/master/StudyPlan/Algo2/day02)          

### 153. Find Minimum in Rotated Sorted Array        
Suppose an array of length n sorted in ascending order is rotated between 1 and n times. For example, the array nums = [0,1,2,4,5,6,7] might become:         
* [4,5,6,7,0,1,2] if it was rotated 4 times.          
* [0,1,2,4,5,6,7] if it was rotated 7 times.          
Notice that rotating an array [a[0], a[1], a[2], ..., a[n-1]] 1 time results in the array [a[n-1], a[0], a[1], a[2], ..., a[n-2]].            
Given the sorted rotated array nums of unique elements, return the minimum element of this array.            
You must write an algorithm that runs in O(log n) time.          

Example 1:         
Input: nums = [3,4,5,1,2]           
Output: 1             
Explanation: The original array was [1,2,3,4,5] rotated 3 times.           

Example 2:         
Input: nums = [4,5,6,7,0,1,2]         
Output: 0           
Explanation: The original array was [0,1,2,4,5,6,7] and it was rotated 4 times.              

Example 3:           
Input: nums = [11,13,15,17]              
Output: 11             
Explanation: The original array was [11,13,15,17] and it was rotated 4 times.            

#### My AC Version           
使用各种库函数轮子解起来非常容易：           
```c++          
class Solution {
public:
    int findMin(vector<int>& nums) {
        if (nums.size() == 0) return nums[0];
        return *(min_element(nums.begin(), nums.end()));
    }
};
```          
Runtime: 0 ms, faster than **100.00%** of C++ online submissions for Find Minimum in Rotated Sorted Array.       
Memory Usage: 10.2 MB, less than 72.74% of C++ online submissions for Find Minimum in Rotated Sorted Array.        

```c++             
class Solution {
public:
    int findMin(vector<int>& nums) {
        if (nums.size() == 0) return nums[0];
        sort(nums.begin(), nums.end());
        return nums[0];
    }
};
```            
Runtime: 0 ms, faster than 100.00% of C++ online submissions for Find Minimum in Rotated Sorted Array.          
Memory Usage: 10.2 MB, less than 72.74% of C++ online submissions for Find Minimum in Rotated Sorted Array.        

尝试着自己写一个不怎么样的二分：           
```c++           
class Solution {
public:
    int findMin(vector<int>& nums) {
        if (nums.size() == 1) return nums[0];
        int left = 0, right = nums.size()-1, middle;
        if (nums[left] < nums[right])   return nums[left];
        while(left < right){
            middle = (left + right) / 2;
            if (nums[left]<nums[middle])
                left = middle;
            else
                right = middle;
        }
        return nums[left+1];
    }
};
```           
Runtime: 0 ms, faster than **100.00%** of C++ online submissions for Find Minimum in Rotated Sorted Array.          
Memory Usage: 10.2 MB, less than 72.74% of C++ online submissions for Find Minimum in Rotated Sorted Array.         
由于 min_element 和 sort 都是不小于线性复杂度的库函数，而我们手写二分则是对数复杂度，故而分析应当效率更高一些。但毕竟库函数已经达到 100% 了，也没有提升空间了。       
由于 while 循环不完善，要首先剔除数组有序和 size = 1 这两种特殊情况，令其直接返回。此时 while 必然具有数组不完全有序的前提条件，于是当值 middle > left，说明前半部分是有序的，最小值出现在后半部分；反则反之。自己对比着测试样例，可以看出来 while 最终停止在最小值的前面的 index 。         

### 162. Find Peak Element            
A peak element is an element that is strictly greater than its neighbors.            
Given an integer array nums, find a peak element, and return its index. If the array contains multiple peaks, return the index to any of the peaks.       
You may imagine that nums[-1] = nums[n] = -∞.        
You must write an algorithm that runs in O(log n) time.         

Example 1:          
Input: nums = [1,2,3,1]         
Output: 2           
Explanation: 3 is a peak element and your function should return the index number 2.           

Example 2:       
Input: nums = [1,2,1,3,5,6,4]        
Output: 5          
Explanation: Your function can return either index number 1 where the peak element is 2, or index number 5 where the peak element is 6.        

Constraints:          
* 1 <= nums.length <= 1000           
* -231 <= nums[i] <= 231 - 1            
* nums[i] != nums[i + 1] for all valid i.

#### My AC Version           
同样的，用轮子很容易解决。但不用动脑子的方法毕竟不能真正锻炼自己。         
```c++        
class Solution {
public:
    int findPeakElement(vector<int>& nums) {
        if(nums.size() ==1) return 0;
        return max_element(nums.begin(), nums.end()) - nums.begin();
    }
};
```          
Runtime: 0 ms, faster than **100.00%** of C++ online submissions for Find Peak Element.           
Memory Usage: 8.7 MB, less than **99.71%** of C++ online submissions for Find Peak Element.        
用线性遍历也容易做，但这个题，怎么玩儿二分？看看讨论区。          

#### Discuss solution           
```c++        
class Solution {
public:
    int findPeakElement(vector<int>& nums) {
        if(nums.size() ==1) return 0;
        int left = 0, right = nums.size()-1, middle;
        while(left < right - 1){
            middle = (left + right) / 2;
            if (nums[middle] > nums[middle-1] && nums[middle] > nums[middle+1])
                return middle;
            else if (nums[middle] > nums[middle-1])
                left = middle;
            else
                right = middle; 
        }
        return nums[left] > nums[right] ? left : right;
    }
    
};
```             
Runtime: 0 ms, faster than **100.00%** of C++ online submissions for Find Peak Element.        
Memory Usage: 8.9 MB, less than 5.13% of C++ online submissions for Find Peak Element.       
这个二分有意思。首先还是先把 size == 1 的特殊情况单独择出来直接返回以节省时间，随后为了在 while 循环里面进行 peak 的判断，要始终保证 middle 左右都有值（避免 middle 出现在两侧的情况）。while 里面先判断一次当前 middle 是不是 peak；如果不是，且 middle 右侧值更大，则右侧必有 peak；左侧同理。改变上下界进入迭代。如果直到 while 无法再进行仍没有选择出 peak，则，peak 必然出现在最后一步迭代更新后的上下界中，直接判断上下界的值返回结果。        

## Day 3 Two Pointers             
[GitHub 连接](https://github.com/OUCliuxiang/leetcode/blob/master/StudyPlan/Algo2/day03)          

### 82. Remove Duplicates from Sorted List II          
Given the head of a sorted linked list, delete all nodes that have duplicate numbers, leaving only distinct numbers from the original list. Return the linked list sorted as well.          

Example 1:        
Input: head = [1,2,3,3,4,4,5]         
Output: [1,2,5]           

Example 2:          
Input: head = [1,1,1,2,3]            
Output: [2,3]            

Constraints:           
* The number of nodes in the list is in the range [0, 300].      
* -100 <= Node.val <= 100        
* The list is guaranteed to be sorted in ascending order.

#### My AC Version      
使用 map 容器记录元素出现的次数。优点是很容易想到也很容易实现，缺点则是空间表现实在堪忧：           
```c++          
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        if(!head || !(head -> next)) return head;
        
        map<int, int> m;
        while(head){
            m[head -> val] ++;
            head = head -> next;
        }
        ListNode *pt1 = new ListNode();
        ListNode *pt2 = pt1;
        for(map<int, int>::iterator iter = m.begin(); 
            iter != m.end(); iter ++){
            if (iter -> second == 1){
                pt2 -> next = new ListNode(iter -> first);
                pt2 = pt2 -> next;
            }
        }
        return pt1 -> next;
    }
};
```          
Runtime: 8 ms, faster than 66.18% of C++ online submissions for Remove Duplicates from Sorted List II.          
Memory Usage: 12 MB, less than 7.64% of C++ online submissions for Remove Duplicates from Sorted List II.          

#### Discuss solution          
讨论区有人提出来使用递归去做。他们的脑子到底是怎么长的，怎么我就想不到？... 

```c++          
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        if(!head || !(head -> next)) return head;
        int val = head -> val;
        ListNode*p = head -> next;
        if (p->val == val){
            while ( p &&  p -> val == val) 
                p = p -> next;
            return deleteDuplicates(p);
        }else{
            head -> next = deleteDuplicates(head -> next);
            return head;
        }
    }
};
```        
Runtime: 4 ms, faster than **94.44%** of C++ online submissions for Remove Duplicates from Sorted List II.          
Memory Usage: 11.1 MB, less than **91.30%** of C++ online submissions for Remove Duplicates from Sorted List II.         
简洁优美，惊为天人。代码理解了，但没有完全理解，只理解了个大概。          

### 15. 3Sum          
Given an integer array nums, return all the triplets [nums[i], nums[j], nums[k]] such that i != j, i != k, and j != k, and nums[i] + nums[j] + nums[k] == 0.       
Notice that the solution set must not contain duplicate triplets.       
Example 1:       
Input: nums = [-1,0,1,2,-1,-4]         
Output: [[-1,-1,2],[-1,0,1]]            

Example 2:        
Input: nums = []         
Output: []        

Example 3:         
Input: nums = [0]      
Output: []           

Constraints:        
* 0 <= nums.length <= 3000     
* -105 <= nums[i] <= 105

#### My AC Version        
```c++         
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        if(nums.size()<3) return {};
        vector<vector<int>> res;
        std::sort(nums.begin(), nums.end());
        for (int i = 0; i < nums.size()-2; i++){
            // printf("%d",i);
            int target = -nums[i];
            int front = i + 1, back = nums.size() - 1;
            while(front < back){
                if (nums[front] + nums[back] > target)
                    back --;
                else if (nums[front] + nums[back] < target)
                    front ++;
                else{
                    printf("%d,%d,%d\n",i, front, back);
                    vector<int> tmp = {nums[i], nums[front], nums[back]};
                    res.emplace_back(tmp);
                    front ++;back --;
                }
            }
            while(i < nums.size()-2 && nums[i] == nums[i+1]) i++;
        }
        std::sort(res.begin(), res.end());
        res.erase(std::unique(res.begin(), res.end()), res.end());
        return res;
    }
};
```        
Runtime: 108 ms, faster than 51.27% of C++ online submissions for 3Sum.         
Memory Usage: 24.8 MB, less than 24.20% of C++ online submissions for 3Sum.        

此题不能用暴力解，否则会 TLE 。但由于给我们的数组存在重复元素，很难准确的避开，所以最后加一个去重复，会占用不小的时间空间资源。讨论区有避开重复元素的方法。      

#### Discuss solution         
```c++           
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        if(nums.size()<3) return {};
        vector<vector<int>> res;
        std::sort(nums.begin(), nums.end());
        for (int i = 0; i < nums.size()-2; i++){
            int target = -nums[i];
            int front = i + 1, back = nums.size() - 1;
            while(front < back){
                if (nums[front] + nums[back] > target)
                    back --;
                else if (nums[front] + nums[back] < target)
                    front ++;
                else{
                    vector<int> tmp = {nums[i], nums[front], nums[back]};
                    res.emplace_back(tmp);
                    while(front < back && nums[front] == tmp[1]) front ++;
                    while(front < back && nums[back] == tmp[2]) back --;
                }
            }
            while(i < nums.size()-2 && nums[i] == nums[i+1]) i++;
        };
        return res;
    }
};
```          
Runtime: 72 ms, faster than 79.40% of C++ online submissions for 3Sum.       
Memory Usage: 21.2 MB, less than 47.92% of C++ online submissions for 3Sum.      
空间时间表现都有较大幅度的提升。         
这个避开重复元素的方法和我们最初使用的 while(...&& nums[front] == nums[front+1]) 类似，但我们是在 `front ++; back --;` 之后才执行上述条件语句，这就造成了判断重复元素的时候，front/back 的值已经改变了。所以，写成如下形式就可以了：          

```c++        
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        if(nums.size()<3) return {};
        vector<vector<int>> res;
        std::sort(nums.begin(), nums.end());
        for (int i = 0; i < nums.size()-2; i++){
            int target = -nums[i];
            int front = i + 1, back = nums.size() - 1;
            while(front < back){
                if (nums[front] + nums[back] > target)
                    back --;
                else if (nums[front] + nums[back] < target)
                    front ++;
                else{
                    vector<int> tmp = {nums[i], nums[front], nums[back]};
                    res.emplace_back(tmp);
                    front ++; back --;
                    while(front < back && nums[front] == nums[front-1]) front ++;
                    while(front < back && nums[back] == nums[back+1]) back --;
                }
            }
            while(i < nums.size()-2 && nums[i] == nums[i+1]) i++;
        }
        return res;
    }
};
```         
Runtime: 64 ms, faster than 88.20% of C++ online submissions for 3Sum.         
Memory Usage: 21.3 MB, less than 43.36% of C++ online submissions for 3Sum.    
时间空间表现非常好。        

## Day 4 Two Pointers             
[GitHub 连接](https://github.com/OUCliuxiang/leetcode/blob/master/StudyPlan/Algo2/day04)          

### 844. Backspace String Compare         
Given two strings s and t, return true if they are equal when both are typed into empty text editors. '#' means a backspace character.       
Note that after backspacing an empty text, the text will continue empty.       

Example 1:          
Input: s = "ab#c", t = "ad#c"            
Output: true            
Explanation: Both s and t become "ac".         

Example 2:          
Input: s = "ab##", t = "c#d#"          
Output: true         
Explanation: Both s and t become "".         

Example 3:          
Input: s = "a##c", t = "#a#c"          
Output: true             
Explanation: Both s and t become "c".          

Example 4:             
Input: s = "a#c", t = "b"           
Output: false            
Explanation: s becomes "c" while t becomes "b".          

Constraints:           
* 1 <= s.length, t.length <= 200              
* s and t only contain lowercase letters and '#' characters.        

#### My AV Version          
```c++        
class Solution {
public:
    bool backspaceCompare(string s, string t) {
        for(int i = 0; i < s.length(); i++){
            if (i > 0 && s[i] == '#'){ 
                s.erase(i-1, 2);
                i-=2;
            }
            while(s[0] == '#') s.erase(0,1);
        }
        for(int i = 0; i < t.length(); i++){
            if (i > 0 && t[i] == '#'){ 
                t.erase(i-1, 2);
                i-=2;
            }
            while(t[0] == '#') t.erase(0,1);
        }
        if (s == t) return true;
        return false;
    }
};
```         
Runtime: 0 ms, faster than **100.00%** of C++ online submissions for Backspace String Compare.            
Memory Usage: 6.2 MB, less than 88.62% of C++ online submissions for Backspace String Compare.            

这题没什么意思。            


### 986. Interval List Intersections           
You are given two lists of closed intervals, firstList and secondList, where firstList[i] = [starti, endi] and secondList[j] = [startj, endj]. Each list of intervals is pairwise disjoint and in sorted order.           
Return the intersection of these two interval lists.           
A closed interval [a, b] (with a <= b) denotes the set of real numbers x with a <= x <= b.            
The intersection of two closed intervals is a set of real numbers that are either empty or represented as a closed interval. For example, the intersection of [1, 3] and [2, 4] is [2, 3].           

Example 1:           
Input: firstList = [[0,2],[5,10],[13,23],[24,25]], secondList = [[1,5],[8,12],[15,24],[25,26]]           
Output: [[1,2],[5,5],[8,10],[15,23],[24,24],[25,25]]            

Example 2:          
Input: firstList = [[1,3],[5,9]], secondList = []             
Output: []          

Example 3:             
Input: firstList = [], secondList = [[4,8],[10,12]]           
Output: []          

Example 4:        
Input: firstList = [[1,7]], secondList = [[3,10]]        
Output: [[3,7]]          

Constraints:           
* 0 <= firstList.length, secondList.length <= 1000          
* firstList.length + secondList.length >= 1           
* 0 <= starti < endi <= 109           
* endi < starti+1           
* 0 <= startj < endj <= 109           
* endj < startj+1

#### My AC Version           
```c++         
class Solution {
public:
    vector<vector<int>> intervalIntersection(vector<vector<int>>& firstList, vector<vector<int>>& secondList) {
        if (firstList.size() == 0 || secondList.size() == 0) return {};
        vector<vector<int>> res;
        int i = 0, j = 0;
        while(i < firstList.size() && j < secondList.size()){
            printf("i = %d, j = %d\t", i, j);
            if (firstList[i][1] < secondList[j][0]) {
                i ++;   printf("1");
            }
            else if (firstList[i][0] > secondList[j][1]) {
                j++;    printf("2");
            }
            
            else if (firstList[i][0] >=secondList[j][0] &&
                     firstList[i][1] <=secondList[j][1]){
                vector<int> tmp = {firstList[i][0], firstList[i][1]};
                res.emplace_back(tmp);
                i++; 
                printf("3");
            }
            else if (firstList[i][0] <=secondList[j][0] &&
                     firstList[i][1] >=secondList[j][1]){
                vector<int> tmp = {secondList[j][0], secondList[j][1]};
                res.emplace_back(tmp);
                j++; 
                printf("4");
            }
            else if (firstList[i][0] <=secondList[j][0] &&
                     firstList[i][1] >=secondList[j][0] &&
                     firstList[i][1] <=secondList[j][1]){
                vector<int> tmp = {secondList[j][0], firstList[i][1]};
                res.emplace_back(tmp);
                i ++;
                printf("5");
            }
            else if (firstList[i][0] >=secondList[j][0] &&
                     firstList[i][0] <=secondList[j][1] &&
                     firstList[i][1] >=secondList[j][1]){
                vector<int> tmp = {firstList[i][0], secondList[j][1]};
                res.emplace_back(tmp);
                j ++;
                printf("6");
            }
            printf("\n");
        }
        return res;
    }
};
```          
Runtime: 44 ms, faster than 33.13% of C++ online submissions for Interval List Intersections.         
Memory Usage: 19.3 MB, less than 20.49% of C++ online submissions for Interval List Intersections.        

暴力求解，while + if else 枚举出所有可能的情况。没什么技巧可言，时间空间表现极差。        

#### Discuss solution          
```c++          
class Solution {
public:
    vector<vector<int>> intervalIntersection(vector<vector<int>>& firstList, vector<vector<int>>& secondList) {
        if (firstList.size() == 0 || secondList.size() == 0) return {};
        vector<vector<int>> res;
        int i = 0, j = 0;
        while(i < firstList.size() && j < secondList.size()){
            int lower = max( firstList[i][0], secondList[j][0]);
            int upper = min( firstList[i][1], secondList[j][1]);
            if (lower <= upper)  res.push_back({lower, upper});
            if (firstList[i][1] > secondList[j][1]) j++;
            else i++;
        }
        return res;
    }
};
```         
使用更精炼的语句遍历了所有可能的情况。自己必写不出来，但比对这测试样例一看代码，还真是这么回事儿......               

### 11. Container With Most Water          
Given n non-negative integers a1, a2, ..., an , where each represents a point at coordinate (i, ai). n vertical lines are drawn such that the two endpoints of the line i is at (i, ai) and (i, 0). Find two lines, which, together with the x-axis forms a container, such that the container contains the most water.             
Notice that you may not slant the container.           
 
Example 1:           
Input: height = [1,8,6,2,5,4,8,3,7]            
Output: 49           
Explanation: The above vertical lines are represented by array [1,8,6,2,5,4,8,3,7]. In this case, the max area of water (blue section) the container can contain is 49.          

Example 2:            
Input: height = [1,1]            
Output: 1           

Example 3:          
Input: height = [4,3,2,1,4]            
Output: 16           

Example 4:         
Input: height = [1,2,1]           
Output: 2          

Constraints:            
* n == height.length           
* 2 <= n <= 105           
* 0 <= height[i] <= 104

#### My TLE Version              
```c++         
class Solution {
public:
    int maxArea(vector<int>& height) {
        int most = 0;
        for(int i = 0; i < height.size(); i++)
            for(int j = i+1; j < height.size(); j++)
                most = max(most, (j-i) * min(height[i], heights[j]));            
    return most;
    }
};
```           
Time Limit Exceeded           
暴力求解，试图遍历出所有可能，时间复杂度 O(n^2) ， TLE 了。          

#### Discuss solution           
```c++          
class Solution {
public:
    int maxArea(vector<int>& height) {
        int most = 0, front = 0, back = height.size()-1;
        while(front < back){
            int h = min(height[front], height[back]);
            most = max(most, ( back - front) * h);
            while(front < back && height[front] <= h) front++;
            while(front < back && height[ back] <= h) back --;
        }
        return most;
    }
};
```          
Runtime: 84 ms, faster than 70.98% of C++ online submissions for Container With Most Water.        
Memory Usage: 59.1 MB, less than 39.17% of C++ online submissions for Container With Most Water.           

nb.     
隐隐约约我也想到了这种方法，但没想出怎样将之具体地实现出来。NB。           