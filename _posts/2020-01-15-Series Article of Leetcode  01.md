---
layout:     post
title:      Series Article of Leetcode Notes -- 01
subtitle:   Study Plan Algo 1        
date:       2021-09-14
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - leetcode      
    - c++     
    - algorithm            
---     

> leetcode 刷题笔记，Study Plan Algorithm 1 。     

## Day 1: Binary Search 二分搜索        
[GitHub 连接](https://github.com/OUCliuxiang/leetcode/blob/master/StudyPlan/Algo1/day1)       

### 704 Binary Search       
Given an array of integers nums which is sorted in ascending order, and an integer target, write a function to search target in nums. If target exists, then return its index. Otherwise, return -1.    
 
You must write an algorithm with O(log n) runtime complexity.
 
Example 1:    
Input: nums = [-1,0,3,5,9,12], target = 9    
Output: 4     
Explanation: 9 exists in nums and its index is 4

Example 2:      
Input: nums = [-1,0,3,5,9,12], target = 2     
Output: -1      
Explanation: 2 does not exist in nums so return -1
  
Constraints:     
1 <= nums.length <= 104    
-104 < nums[i], target < 104
All the integers in nums are unique.   
nums is sorted in ascending order.

#### My AC version:    
```c++           
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int length = nums.size();
        int rest = length;
        int upper = length, under = 0;
        int nowIndex = length / 2;
        int nowValue = nums[nowIndex];
        if ( nowValue == target) {
            return nowIndex;
        }
        rest /= 2;

        while (rest){
            if (target > nowValue){
                under = nowIndex;
                nowIndex = (nowIndex + upper)/2;
            }
            else{
                upper = nowIndex;
                nowIndex = (nowIndex + under) /2;
            }
            nowValue = nums[nowIndex];
            if (nowValue == target){
                return nowIndex;
            }
            rest/=2;
        }
        return -1;
    }
};
```       
Runtime: 40 ms, faster than 49.60% of C++ online submissions for Binary Search. 速度只战胜了 49.6% 的提交，不太行。        
Memory Usage: 27.4 MB, less than 99.80% of C++ online submissions for Binary Search. 空间占用战胜了 99.8% 的提交，这？           

极其朴素的二分思想，在进入循环之前进行一次判断，如果条件符合直接返回；通过 `while(rest /= 2)` 保证循环最大执行 log n 次；循环内根据目标数与当前值的大小判断交换 upper 上界和 under 下界，迭代循环；循环内进行判断没条件符合直接返回；如果循环内未返回而结束，必然说明不存在符合条件的数，返回 -1。     

#### Official solution    
```c++    
int search_official(vector<int>& nums, int target) {
    int pivot, left = 0, right = nums.size() - 1;
    while (left <= right) {
        pivot = left + (right - left) /2;
        if (nums[pivot] == target) 
            return pivot;
        if (target < nums[pivot]) 
            right = pivot - 1;
        else left = pivot + 1;
        }
    return -1;
}
```     
出现在题目 Solution 中的代码，速度比我自己的代码稍快，主要是思想思路比我要简洁许多。      
上下界一定要写，这个解法直接给出一个 pivot 作为锚点（中点），中点更新为下界加上下界差的二分之一；其实很容易想，但我没想到。     
目标值大于锚点值，则上界不变，下界直接锚点加一。     
反之，下界不变，上界直接锚点减一。       
如果到了上下界相等时还找不到目标，下一次更新上下界，会出现上界比下界还小的情况，退出循环，这一点比较难想。      
但以界值作为判断条件，很简洁。     

### 278 First Bad Version         
You are a product manager and currently leading a team to develop a new product. Unfortunately, the latest version of your product fails the quality check. Since each version is developed based on the previous version, all the versions after a bad version are also bad.      

Suppose you have n versions [1, 2, ..., n] and you want to find out the first bad one, which causes all the following ones to be bad. You are given an API bool isBadVersion(version) which returns whether version is bad. Implement a function to find the first bad version.     
You should minimize the number of calls to the API.

 
Example 1:     
Input: n = 5, bad = 4     
Output: 4     
Explanation:     
call isBadVersion(3) -> false     
call isBadVersion(5) -> true     
call isBadVersion(4) -> true     
Then 4 is the first bad version.     

Example 2:     
Input: n = 1, bad = 1      
Output: 1     

Constraints:     
1 <= bad <= n <= 231 - 1      

#### My AC version      

```c++     
// The API isBadVersion is defined for you.     
// bool isBadVersion(int version);     

class Solution {
public:
    int firstBadVersion(int n) {
        int under=1, upper=n, target=n;
        
        while ( under <= upper){
            
            target = under + (upper - under) / 2;
            
            if ( target == 1 and isBadVersion( target) )
                return target;
            
            if ( isBadVersion( target)){
                if ( !isBadVersion( target - 1)){
                    return target;
                }
                else{
                    upper = target - 1;
                }
            }
            else{
                if ( isBadVersion( target + 1)){
                    return target + 1;
                }
                else{
                    under = target + 1;
                }
            }
        }
        return 0;
    }
};
```     
Runtime: 0 ms, faster than 100.00% of C++ online submissions for First Bad Version.       
Memory Usage: 6 MB, less than 21.66% of C++ online submissions for First Bad Version.      
其实这种简单题大家都差不多，相同的代码提交几次从 0 ms 到 5 ms ，5.8 MB 到 6.0 MB 不等，相差不大。    
这个题也是经典的二分搜索，比着上面一个题的葫芦画瓢就行。     
设置一个上下界，target 从 n/2 开始，更新上下界，直到找到符合条件的数或上下界矛盾为止。中间把 `n=1, target = 1` 这个情况拎出来单独讨论。       

#### Official Solution      
出现在 Solution 中的解法相当简洁，先贴代码：      
```c++     
public int firstBadVersion(int n) {
    int left = 1;
    int right = n;
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (isBadVersion(mid)) {
            right = mid;
        } else {
            left = mid + 1;
        }
    }
    return left;
}
```        
官方版本非常巧妙地利用了 c++ 整数除法向下取整的特性。考虑两种情况：     
1. 当 n=1，left = middle = right，不进入迭代，直接返回 left = 1 。       
2. 当 n>1，迭代终点的起始条件只能是 left + 1 = right，left = G, right=B；此时 mid = left， mid = G --> left += 1 = right 回到了条件 1，迭代结束，返回 left。     
非常巧妙。     

### 35 Search Insert Position     

Given a sorted array of distinct integers and a target value, return the index if the target is found. If not, return the index where it would be if it were inserted in order.

You must write an algorithm with O(log n) runtime complexity.

Example 1:
Input: nums = [1,3,5,6], target = 5     
Output: 2

Example 2:
Input: nums = [1,3,5,6], target = 2      
Output: 1

Example 3:
Input: nums = [1,3,5,6], target = 7     
Output: 4

Example 4:
Input: nums = [1,3,5,6], target = 0      
Output: 0

Example 5:
Input: nums = [1], target = 0        
Output: 0

 

Constraints:     
1 <= nums.length <= 104      
-104 <= nums[i] <= 104      
nums contains distinct values sorted in ascending order.     
-104 <= target <= 104      

#### My AC version     
```c++     
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        int under = 0, upper = nums.size()-1;
        if (target > nums.at(upper))
            return upper + 1;
        if (target <= nums.at(under))
            return under;
        while(under <= upper){
            int mid = under + (upper - under) / 2;
            if (target <= nums.at(mid)){
                if (target > nums.at(mid - 1))
                    return mid;
                else
                    upper = mid;
            }
            else
                under = mid + 1;
        }
        return 0;
    }
};
```      
Runtime: 4 ms, faster than 79.59% of C++ online submissions for Search Insert Position.       
Memory Usage: 9.6 MB, less than 55.48% of C++ online submissions for Search Insert Position.      
没什么好说的，中规中矩的解法，先把小于最小和大于最大两个特殊情况单独择出来，然后递归解决。       
时间和空间表现不算优秀，看看讨论区解法。      

#### Discuss solution      
本题 Solution 需要花钱解锁，但讨论区里有很多大神。贴一个讨论区里出现的极其简洁的实现：    
```c++     
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        vector<int>::iterator lower; 
        lower = lower_bound (nums.begin(), nums.end(), target); 
        return (lower-nums.begin());
    }
};
```     
总共三行代码，使用了来自 c++20 的新特性（无怪翻了半天 cpp standard 和 c++ primer 没找到）。lower_bound 的功能正如其函数名，寻找一个变量在一个容器内的下界，返回一个迭代器。    
具体的，看来自 [cplusplus](https://www.cplusplus.com/reference/algorithm/lower_bound/) 的示例代码：     
```c++     
// lower_bound/upper_bound example
#include <iostream>     // std::cout
#include <algorithm>    // std::lower_bound, std::upper_bound, std::sort
#include <vector>       // std::vector

int main () {
int myints[] = {10,20,30,30,20,10,10,20};
std::vector<int> v(myints,myints+8);           // 10 20 30 30 20 10 10 20

std::sort (v.begin(), v.end());                // 10 10 10 20 20 20 30 30

std::vector<int>::iterator low,up;
low=std::lower_bound (v.begin(), v.end(), 20); //          ^
up= std::upper_bound (v.begin(), v.end(), 20); //                   ^

std::cout << "lower_bound at position " << (low- v.begin()) << '\n';
std::cout << "upper_bound at position " << (up - v.begin()) << '\n';

return 0;
}
```      
Output:     
```
lower_bound at position 3    
upper_bound at position 6     
```
更实际一点，就是在一个（有序）容器内插入一个值，返回这个值应当位于容器的所有合适的位置的最前面的迭代器。简直就是为这个题量身设计的。     


## Day 2: Two Pointers 双指针        
[GitHub 连接](https://github.com/OUCliuxiang/leetcode/blob/master/StudyPlan/Algo1/day2)       

### 977 Squares of a Soarted Array         

Given an integer array nums sorted in non-decreasing order, return an array of the squares of each number sorted in non-decreasing order.    

Example 1:       
Input: nums = [-4,-1,0,3,10]       
Output: [0,1,9,16,100]      
Explanation: After squaring, the array becomes [16,1,0,9,100].        
After sorting, it becomes [0,1,9,16,100].        

Example 2:        
Input: nums = [-7,-3,2,3,11]      
Output: [4,9,9,49,121]      

Constraints:      
1 <= nums.length <= 104      
-104 <= nums[i] <= 104       
nums is sorted in non-decreasing order.     
Follow up: Squaring each element and sorting the new array is very trivial, could you find an O(n) solution using a different approach?
 
#### My AC version     
```c++      
class Solution {
public:
    vector<int> sortedSquares(vector<int>& nums) {
        for (int i = 0; i < nums.size(); i++)
            nums[i] *= nums[i];
        sort(nums.begin(), nums.end());
        return nums;
    }
};
```       
Runtime: 36 ms, faster than 50.74% of C++ online submissions for Squares of a Sorted Array.      
Memory Usage: 25.9 MB, less than 55.72% of C++ online submissions for Squares of a Sorted Array.      

简洁朴素。先使用迭代器（指针）使 vector 元素平方，再利用 algorithm 算法库中的 sort 方法排序。没什么技术性，看看讨论区有什么奇技淫巧。       

#### Discuss solution      

这，讨论区就有点强行双指针了。行吧，虽然有其他解法，但这个题毕竟考的是双指针。    
```c++      
class Solution {
public:
    vector<int> sortedSquares(vector<int>& A) {
        vector<int> res(A.size());
        int l = 0, r = A.size() - 1;
        for (int k = A.size() - 1; k >= 0; k--) {
            if (abs(A[r]) > abs(A[l])) res[k] = A[r] * A[r--];
            else res[k] = A[l] * A[l++];
        }
        return res;
    }
};
```       
Runtime: 43 ms, faster than 30.28% of C++ online submissions for Squares of a Sorted Array.      
Memory Usage: 25.9 MB, less than 82.08% of C++ online submissions for Squares of a Sorted Array.       
也是比较朴素的思路，既然原始数组已经是排好序的，只是正负值不一致。那只需要在数组收尾各放置一个指针，比较首尾指针指向值的 abs 大小就可，从大到小，在新数组从右到左排布。可是这样需要新建一个数组作为 result，额外占据一个数组空间，真的好吗？      

### 189 Rotate Array       

Given an array, rotate the array to the right by k steps, where k is non-negative.

Example 1:     

Input: nums = [1,2,3,4,5,6,7], k = 3      
Output: [5,6,7,1,2,3,4]     
Explanation:      
rotate 1 steps to the right: [7,1,2,3,4,5,6]      
rotate 2 steps to the right: [6,7,1,2,3,4,5]      
rotate 3 steps to the right: [5,6,7,1,2,3,4]      

Example 2:      

Input: nums = [-1,-100,3,99], k = 2      
Output: [3,99,-1,-100]       
Explanation:       
rotate 1 steps to the right: [99,-1,-100,3]       
rotate 2 steps to the right: [3,99,-1,-100]      

Constraints:       

1 <= nums.length <= 105      
-231 <= nums[i] <= 231 - 1       
0 <= k <= 105      

Follow up:      

Try to come up with as many solutions as you can. There are at least three different ways to solve this problem.       
Could you do it in-place with O(1) extra space?        

```c++     
class Solution {
public:
    void rotate(vector<int>& nums, int k) {
        k = k % nums.size();
        vector<int> res(nums.begin(), nums.end()-k);// = nums;
        copy(res.end() - k, res.end(), nums.begin());
        copy(res.begin(), res.end()-k, nums.begin() + k);
    }
};
```     
Runtime: 28 ms, faster than 73.06% of C++ online submissions for Rotate Array.       
Memory Usage: 25.7 MB, less than 11.07% of C++ online submissions for Rotate Array.       

中规中矩的解法，需要考虑一下是否 k > nums.size()，通过取余操作避免这种情况；方便起见，对 nums.size() 取余之后的值仍以 k 表示。随后就好说了，所谓的 array rotate 实际上就变成了：     
将原数组从后往前数 k 个元素保持顺序不变平移到最前端；      
或       
将原数组从前往后数 size()-k 个元素平移到数组尾端；    
于是构造一个新 vector 接受原 vector 的值，然后分两次给原 vector 赋值即可。    

其实有另一个空间复杂度稍低的解法：     
```c++    
class Solution {
public:
    void rotate(vector<int>& nums, int k) {
        k = k % nums.size();
        int size = nums.size();
        nums.insert(nums.begin(), nums.end() - k, nums.end());
        nums.resize(size);
    }
};
```     
但是，但是，不知道哪里出了问题，有时候插入的值老是在目标值之前。好像是某些情况下 迭代器 .end() 的位置被移动了。又但是，如果使用 stdout 打印 end()-1 处的值，并没有发生移位。难道偏偏在 insert处发生改变？       

即使不出错误，这两种做法显然也都达不到空间上 O(1)，看讨论区吧。     

#### Discuss solultion      

```c++    
class Solution {
public:
    void rotate(vector<int>& nums, int k) {
        int n = nums.size();
        reverse(nums.begin(), nums.end());
        reverse(nums.begin(), nums.begin() + k%n);
        reverse(nums.begin()+ k%n, nums.end());
    }
};
```     

Runtime: 24 ms, faster than 89.99% of C++ online submissions for Rotate Array.     
Memory Usage: 24.8 MB, less than 98.91% of C++ online submissions for Rotate Array.     

WOC！     
讨论区里出大神，都些什么奇技淫巧......        

## Day 3: Two Pointers 双指针        
[GitHub 连接](https://github.com/OUCliuxiang/leetcode/blob/master/StudyPlan/Algo1/day3)       

### 283 Move Zeroes        

Given an integer array nums, move all 0's to the end of it while maintaining the relative order of the non-zero elements.     
Note that you must do this in-place without making a copy of the array.        

Example 1:       
Input: nums = [0,1,0,3,12]     
Output: [1,3,12,0,0]     

Example 2:     
Input: nums = [0]      
Output: [0]     

Constraints:     
1 <= nums.length <= 104       
-231 <= nums[i] <= 231 - 1       

Follow up: Could you minimize the total number of operations done?     
#### My AC version       
```c++    
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int count = 0;
        for (vector<int>::iterator it = nums.begin(); it != nums.end(); it ++){
            if (*it == 0){
                nums.erase(it);
                count ++;
                it -- ;
            }
        }
        nums.insert(nums.end(), count, 0);
    }
};
```

Runtime: 8 ms, faster than 38.59% of C++ online submissions for Move Zeroes.      
Memory Usage: 8.8 MB, less than 85.11% of C++ online submissions for Move Zeroes.       

中规中矩的解法，没什么特色。由于难以使用交换操作，这个题本质上就是删除 vector 里值为零的元素，在 vector 尾端插入与删除操作相等数量的 0 。       
找找讨论区又有什么奇技淫巧。       

#### Discuss solution      

没什么好看的，讨论区的解法不如我的好。      

### 167 Two Sum II - Input array is sorted     

Given an array of integers numbers that is already sorted in non-decreasing order, find two numbers such that they add up to a specific target number.      
Return the indices of the two numbers (1-indexed) as an integer array answer of size 2, where 1 <= answer[0] < answer[1] <= numbers.length.       
The tests are generated such that there is exactly one solution. You may not use the same element twice.      

Example 1:        
Input: numbers = [2,7,11,15], target = 9
Output: [1,2]       
Explanation: The sum of 2 and 7 is 9. Therefore index1 = 1, index2 = 2.        

Example 2:          
Input: numbers = [2,3,4], target = 6       
Output: [1,3]        

Example 3:        
Input: numbers = [-1,0], target = -1       
Output: [1,2]        

Constraints:         
2 <= numbers.length <= 3 * 104      
-1000 <= numbers[i] <= 1000      
numbers is sorted in non-decreasing order.        
-1000 <= target <= 1000        
The tests are generated such that there is exactly one solution.      

#### My AC Version
```c++    
class Solution {
public:
    vector<int> twoSum(vector<int>& numbers, int target) {
        vector<int> res(2);
        for (int i = 0; i < numbers.size(); i ++ ){
            int under = i + 1, upper = numbers.size()-1;
            while (under <= upper){
                int j = under + (upper - under) / 2;
                if (numbers.at(i) + numbers.at(j) == target){
                    res[0] = i + 1;
                    res[1] = j + 1;
                    return res;
                }
                else if(numbers.at(i) + numbers.at(j) > target){
                    upper = j - 1;
                }
                else{
                    under = j + 1;
                }
            }
        }
        return res;
    }
};
```        
Runtime: 12 ms, faster than 9.56% of C++ online submissions for Two Sum II - Input array is sorted.     
Memory Usage: 9.6 MB, less than 45.72% of C++ online submissions for Two Sum II - Input array is sorted.      


平平无奇的解法，使用双循环  O(n^2) 的解法报 Time Limit Exceeded 了，索性换成 Binary Search O(nlogn) 的解法。没有任何特色。找找讨论区有什奇技淫巧。      

#### Discuss solution      

```c++    
class Solution {     
public:    
vector<int> twoSum(vector<int>& numbers, int target) {
    int lo=0, hi=numbers.size()-1;
    while (numbers[lo]+numbers[hi]!=target){
        if (numbers[lo]+numbers[hi]<target){
            lo++;
        } else {
            hi--;
        }
    }
    return vector<int>({lo+1,hi+1});
}
};
```    
一个时间复杂度 O(n) 的解法，非常简洁优雅。一小一大两个 pointer ，结果小了就加小，大了就减大。挺容易想，就是没想到，嘿。     

## Day 4: Two Pointers 双指针        
[GitHub 连接](https://github.com/OUCliuxiang/leetcode/blob/master/StudyPlan/Algo1/day4)        

### 344. Reverse String        

Write a function that reverses a string. The input string is given as an array of characters s.      

Example 1:      
Input: s = ["h","e","l","l","o"]     
Output: ["o","l","l","e","h"]      

Example 2:       
Input: s = ["H","a","n","n","a","h"]      
Output: ["h","a","n","n","a","H"]      

Constraints:     
1 <= s.length <= 105       
s[i] is a printable ascii character.        

Follow up: Do not allocate extra space for another array. You must do this by modifying the input array in-place with O(1) extra memory.     
#### My AC Version      

**first solution:**     
```c++       
class Solution {
public:
    void reverseString(vector<char>& s) {
        for (vector<char>::iterator left = s.begin(), right = s.end()-1; 
             right >= left; right --, left ++){
            iter_swap(left, right);
        }
    }
};
```       
Runtime: 23 ms, faster than 54.21% of C++ online submissions for Reverse String.      
Memory Usage: 23 MB, less than 94.14% of C++ online submissions for Reverse String.       
平平无奇毫无创意的两行解法。iter_swap 这个东西应该是 c++20 新标准，方便，真方便。      

**second solution:**     
```c++      
class Solution {
public:
    void reverseString(vector<char>& s) {
        reverse(s.begin(), s.end());
    }
};
```       
Runtime: 16 ms, faster than 94.27% of C++ online submissions for Reverse String.
Memory Usage: 23.3 MB, less than 41.43% of C++ online submissions for Reverse String.       

平平无奇的一行解法， 来自 algorithm 库的 reverse 方法简直为这个题量身定制的。
     

**third solution:**     
```c++
class Solution {
public:
    void reverseString(vector<char>& s) {
        vector<char> st( s.rbegin(), s.rend());
        s = st;
    }
};
```     
Runtime: 24 ms, faster than 53.10% of C++ online submissions for Reverse String.       
Memory Usage: 23.5 MB, less than 5.14% of C++ online submissions for Reverse String.       

使用逆序迭代器 rbegin()（从后往前数的第一个，也就是最后一个，方向向前，或者说 执行++ 会向前移） 和 rend()（从后往前数最后一个之后，也就是第一个元素再前一个，方向向前，也就是说执行 -- 会向后走），赋值新数组，再反向赋值回去。     
好方便。

找一找讨论区的奇技淫巧。      

#### Discuss solution      
霍～糟了，我成奇技淫巧了。      

### 557. Reverse Words in a String III       
Given a string s, reverse the order of characters in each word within a sentence while still preserving whitespace and initial word order.      

Example 1:     
Input: s = "Let's take LeetCode contest"      
Output: "s'teL ekat edoCteeL tsetnoc"      

Example 2:       
Input: s = "God Ding"     
Output: "doG gniD"      

Constraints:
1 <= s.length <= 5 * 104      
s contains printable ASCII characters.      
s does not contain any leading or trailing spaces.      
There is at least one word in s.      
All the words in s are separated by a single space.       

#### My AC Version       

```c++     
class Solution {
public:
    string reverseWords(string s) {
        int found = -1;
        while (found != s.length()){
            int wordEnd = s.find_first_of(' ', found + 1);
            wordEnd = (wordEnd == -1 ? s.length() : wordEnd);
            reverse(s.begin()+found+1, s.begin()+wordEnd);
            found = wordEnd;
        }
        return s;
    }
};
```      
Runtime: 11 ms, faster than **94.91%** of C++ online submissions for Reverse Words in a String III.      
Memory Usage: 9.5 MB, less than **99.18%** of C++ online submissions for Reverse Words in a String III.      

中规中矩的解法，但是时间和空间表现还挺优秀的。具体思想就是在一个 String 中使用 `find_first_of()` 方法找出每一个空格分开的单词，使用 algorithm 库中的 `reverse()` 方法在原句子中使用指针，对每个单词直接做翻转操作。      
看看讨论区有什么奇技淫巧。       

#### Discuss solution      

没什么看的，讨论区不如我。     
