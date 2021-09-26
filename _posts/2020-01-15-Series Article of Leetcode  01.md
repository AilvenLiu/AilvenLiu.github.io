---
layout:     post
title:      Series Article of Leetcode Notes -- 01
subtitle:   Study Plan Algo 1 Day 1-7       
date:       2021-09-14
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - leetcode      
    - c++     
    - algorithm            
---     

> leetcode 刷题笔记，Study Plan Algorithm 1, Day 1 -- Day 7 。     

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

## Day 5: Two Pointers 双指针        
[GitHub 连接](https://github.com/OUCliuxiang/leetcode/blob/master/StudyPlan/Algo1/day5)        

###  876. Middle of the Linked List          
Given the head of a singly linked list, return the middle node of the linked list.        
If there are two middle nodes, return the second middle node.        

Example 1:       
Input: head = [1,2,3,4,5]       
Output: [3,4,5]       
Explanation: The middle node of the list is node 3.       

Example 2:       
Input: head = [1,2,3,4,5,6]       
Output: [4,5,6]        
Explanation: Since the list has two middle nodes with values 3 and 4, we return the second one.        

Constraints:       
The number of nodes in the list is in the range [1, 100].       
1 <= Node.val <= 100      

#### My AC version       

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
    ListNode* middleNode(ListNode* head) {
        ListNode* node = head;
        int length = 1;
        while(node -> next){
            length ++;
            node = node -> next;
        }
        length /= 2;
        length += 1;
        for(int i = 1; i < length; i++){
            head = head -> next;
        }
        return head;
    }
};
```      
Runtime: 0 ms, faster than **100.00%** of C++ online submissions for Middle of the Linked List.       
Memory Usage: 7 MB, less than **87.01%** of C++ online submissions for Middle of the Linked List.       

平平无奇循规蹈矩的解法，先 while 循环获取长度，然后 for 循环走到链表中间点。值得注意的一点是 c++ 中点操作符 `.` 和箭头操作符 `->` 的使用。由于 head 和 node 是结构体**指针**，其值是指向结构体内存地址首部的一串地址数据，只能使用箭头操作符 `node -> item` 获取成员变量；其效果和 `(*node).item`， 先使用 `*` 从结构体指针获取成员变量，再用点操作符 `(*node).item` 获取成员变量，是一样的。      
看看讨论区有什么奇技淫巧。       

#### Discuss solution      
还真有个其妙的解法，名为 Slow and Fast Pointers 快慢指针。具体思想是对同一个链表构造两个指针，由 head 开始，slow pointer 每走一步，fast pointer 走两步。直到 fast pointer 或 fast pointer 的 next node 越界，此时 slow pointer 的位置正好是 middle：      
```c++     
ListNode* middleNode(ListNode* head) {
    ListNode *slow = head, *fast = head;
    while (fast && fast->next)
        slow = slow->next, fast = fast->next->next;
    return slow;
    }
```        
长知识，涨知识。     

### 19. Remove Nth Node From End of List      
Given the head of a linked list, remove the nth node from the end of the list and return its head.         

Example 1:       
Input: head = [1,2,3,4,5], n = 2        
Output: [1,2,3,5]        
 
Example 2:       
Input: head = [1], n = 1      
Output: []      

Example 3:       
Input: head = [1,2], n = 1       
Output: [1]

Constraints:     
The number of nodes in the list is sz.       
1 <= sz <= 30       
0 <= Node.val <= 100      
1 <= n <= sz      

Follow up: Could you do this in one pass?

#### My AC version      

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
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        ListNode* node = head;
        int length = 0;
        while(node){
            length ++;
            node = node -> next;
        }
        length -= n;
        if (length == 0){
            head = head -> next;
        }
        else{
            node = head;
            for (int i = 1; i < length; i++){
                node = node -> next;
            }
            node -> next = node -> next -> next;
        }
        return head;
    }
};
```       
Runtime: 4 ms, faster than 79.56% of C++ online submissions for Remove Nth Node From End of List.       
Memory Usage: 10.8 MB, less than 31.05% of C++ online submissions for Remove Nth Node From End of List.       

循规蹈矩平平无奇的解法，整体过程和上一题差不多。注意的点是将需要删除首部元素的情况和其他情况分开讨论。        
看看讨论区有什么奇技淫巧。       

#### Discuss solution      

没什么看的，讨论区高赞解法就两种类型，一种和我差不多，另一种快慢指针，有点儿难理解，不费脑子了。    

## Day 6: Sliding Window 滑动窗口                 
[GitHub 连接](https://github.com/OUCliuxiang/leetcode/blob/master/StudyPlan/Algo1/day6)           

### 3. Longest Substring Without Repeating Characters      

Given a string s, find the length of the longest substring without repeating characters.          

Example 1:            
Input: s = "abcabcbb"       
Output: 3          
Explanation: The answer is "abc", with the length of 3.         

Example 2:            
Input: s = "bbbbb"               
Output: 1           
Explanation: The answer is "b", with the length of 1.            

Example 3:            
Input: s = "pwwkew"             
Output: 3            
Explanation: The answer is "wke", with the length of 3.           

Notice that the answer must be a substring, "pwke" is a subsequence and not a substring.

Example 4:        
Input: s = ""        
Output: 0            

Constraints:            
0 <= s.length <= 5 * 104            
s consists of English letters, digits, symbols and spaces.           

#### My Version    
没有，没做出来。本科数据结构课上老师讲过这个类型题，当时不会做，现在还不会做。直接看讨论区。           

#### Discuss solution      
```c++      
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        vector<int> dict(256, -1);
        int start = -1, maxLen = 0;
        for(int i = 0; i < s.length(); i++){
            if (dict[s[i]] > start)
                start = dict[s[i]];
            dict[s[i]] = i;
            maxLen = max(maxLen, i-start);
        }
        return maxLen;
    }
};
```         
Runtime: 10 ms, faster than 74.17% of C++ online submissions for Longest Substring Without Repeating Characters.       
Memory Usage: 8.2 MB, less than 79.99% of C++ online submissions for Longest Substring Without Repeating Characters.            

说实在的，comments 里面给爷整笑了，一堆刷 niubi, 666 的，还有人在下面 you make me laugh laoge。除此之外，最多的评论还是 amazing 。的确是个令人拍案叫绝的解法。       
comments 里面给了一个解释，如下：          
"dict" is used to keep tracking the char in the input string you read every time. start indicates the position of starting position of the substring. At the beginning, it initializes all value in "dict" to -1. Then in the for loop, it scans every char in the string. If the char in the "dict"'s value is larger than "start", it means it already in the substring. You should change the start position of substring to the repeat position and start a new count. "maxLen" records the maximum length of substring you have so far.      
  
For example, the input is "aba", you check dict\[s\[0\]\], which is dict\[97\] is -1. Therefore, you can change the dict\[97\] to 0. In this way, you keep recording the string char's position in the dict. When you meet the second "a" in the input, dict\[97\] is 0 and larger than start, which is -1. Then you change start value to 0. When you apply length function ( i - start), you calculate the new substring length, which didn't contain the substring before the first "a".     

还是不太容易理解，可能是英文表述本身就给理解加了一层障碍吧。我尝试用中文做个解释：    
dict 是一个长度为 256 的 vector ，这是由于 ASCII 决定了 string 中所有可能出现的元素种类总量为 256。现在开始遍历字符串 s，dict 的每一个位置（按照ASCII顺序）记录着当前字符上一次出现的 index，start 则记录着最近一个已经出现过的字符上一次出现的 index，默认状态为 -1。      
如果当前字符没有出现过，dict 相应位置保持默认状态为 -1；如果已经出现过，也即 dict 相应位置的值发生过改变，大于上一次更新或默认的 start，则将 start 更新为该元素上一次出现的 index 。          

需要考虑一种特殊情况：abba 型。使用文字进行表述既是，在当前重复的元素及其上一次出现的位置之间，有另一组不同的重复元素。此时我们不能简单的更新 start 到当前元素上一次出现的位置，因为中间隔着一组已经重复了的元素。需要保持为上一组重复元素的上一次出现 index 。于是，代码中 if 语句写为 `if ( dict[s[i]] > start)` 只有当当前元素上一次出现的 index 大于（表现在 string 顺序上也即后于） 上一组重复元素的上一个出现 index 时，才更新。           
 
比较难解释，多看代码多理解。       

comments 还提出一种改进型的解法。一方面使用了 map 代替一次性声明 256 个存储空间的 vector ，随用随加，节省了空间；另一方面，使用 max 语法更新 maxLen 更方便理解：     

```c++        
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        map<char, int> charMap;
        int start = -1, maxLen = 0;
        for(int i = 0; i < s.length(); i++){
            if (charMap.count(s[i]) != 0)
                start = max(charMap[s[i]], start);
                // 当前元素上一次出现的位置与上一组重复元素 
                // 倒数第二次出现的位置做个比较
            charMap[s[i]] = i;
            maxLen = max(maxLen, i-start);
        }
        return maxLen;
    }
};
```          
Runtime: 11 ms, faster than 73.65% of C++ online submissions for Longest Substring Without Repeating Characters.           
Memory Usage: 8.4 MB, less than 58.06% of C++ online submissions for Longest Substring Without Repeating Characters.            

相对而言是容易理解了些，但节省了哪门子的空间？           

### 567. Permutation in String           
Given two strings s1 and s2, return true if s2 contains a permutation of s1, or false otherwise.            
In other words, return true if one of s1's permutations is the substring of s2.            

Example 1:          
Input: s1 = "ab", s2 = "eidbaooo"           
Output: true           
Explanation: s2 contains one permutation of s1 ("ba").            

Example 2:             
Input: s1 = "ab", s2 = "eidboaoo"           
Output: false               

Constraints:                  

1 <= s1.length, s2.length <= 104               
s1 and s2 consist of lowercase English letters.                

#### My TLE Version         
```c++     
class Solution {
public:
    bool checkInclusion(string s1, string s2) {
        vector<string> permutations;
        sort(s1.begin(), s1.end());
        do{
            permutations.push_back(s1);
        }while(next_permutation(s1.begin(), s1.end()));
        for (vector<string>::iterator it = permutations.begin(); it != permutations.end(); it++){
            if (s2.find(*it) != -1){
                return true;
            }
        }
        return false;
    }                

    bool checkInclusion_second(string s1, string s2) {
        sort(s1.begin(), s1.end());
        for (int i = 0; i < s2.length()-s1.length()+1; i ++){
            string tmp = s2.substr(s2.begin()+i, s1.length());
            sort(tmp.begin(), tmp.end());
            if (s1 == tmp)
                return true;
        }
        return false;
    }
};
```         
尝试用枚举的方式暴力求解，失败。本题超出自己知识范围了，看看讨论区有什么奇技淫巧。         

#### Discuss solution            

```c++     
class Solution {
public:
    bool checkInclusion(string s1, string s2) {
        vector<int> curr(26), goal(26);
        for (char c: s1)    goal[c - 'a'] ++;
        for (int i = 0; i < s2.length(); i ++){
            curr[s2[i] - 'a'] ++;
            if (i >= s1.length())    curr[s2[i - s1.length()] - 'a'] --;
            if (curr == goal) return true;
        }
        return false;
    }
};
```        
Runtime: 8 ms, faster than 80.66% of C++ online submissions for Permutation in String.      
Memory Usage: 7.3 MB, less than 76.83% of C++ online submissions for Permutation in String.       

好巧妙的方法，我怎么就没想到...？      
这个题既然出现在了 Slide Windows 大类下，自然应当考虑滑动窗口解法。由于题目已经限制了 s1, s2 的元素取值范围是 lowercase English letters ，我们设置两个容量为 26 的 vector\<int\>  型容器。一个是 goal，储存待模式字符串 s1 包含的诸字符的数量；一个是 curr，储存待匹配字符串 s2 最近 s1.length() 长度子串中包含的诸字符的数量。           
其本质上就是给 s2 一个长度为 s1.length() 滑动窗口 slide windows，比较滑动窗口中储存的各字符出现次数和 goal 是否相等。         
比较难以具象解释，多看代码，慢慢悟就好了。        
需要注意的是，不能使用类似 `int goal[26]` 形式的数组。这是由于使用 `==` 判断数组相等实质比较的是地址相等，两个不同数组永远为 false；而 vector 则重载了 `==` 操作符，为逐元素比较内容（见《C++标准库（第二版）》电子工业出版社中译本 273 页）。          

## Day 7 BFS / DFS 广度优先/深度优先搜索             
[GitHub 连接](https://github.com/OUCliuxiang/leetcode/blob/master/StudyPlan/Algo1/day7)

### 733. Flood Fill         

An image is represented by an m x n integer grid image where image\[i\]\[j\] represents the pixel value of the image. You are also given three integers sr, sc, and newColor. You should perform a flood fill on the image starting from the pixel image\[sr\]\[sc\].         
To perform a flood fill, consider the starting pixel, plus any pixels connected 4-directionally to the starting pixel of the same color as the starting pixel, plus any pixels connected 4-directionally to those pixels (also with the same color), and so on. Replace the color of all of the aforementioned pixels with newColor. Return the modified image after performing the flood fill.             
 
Example 1:             
Input: image = \[\[1,1,1\],\[1,1,0\],\[1,0,1\]\], sr = 1, sc = 1, newColor = 2           
Output: \[\[2,2,2\],\[2,2,0\],\[2,0,1\]\]           
Explanation: From the center of the image with position (sr, sc) = (1, 1) (i.e., the red pixel), all pixels connected by a path of the same color as the starting pixel (i.e., the blue pixels) are colored with the new color.          
Note the bottom corner is not colored 2, because it is not 4-directionally connected to the starting pixel.        

Example 2:          
Input: image = [[0,0,0],[0,0,0]], sr = 0, sc = 0, newColor = 2          
Output: [[2,2,2],[2,2,2]]           

Constraints:
 * m == image.length      
 * n == image[i].length         
 * 1 <= m, n <= 50        
 * 0 <= image[i][j], newColor < 216        
 * 0 <= sr < m         
 * 0 <= sc < n          

#### My AC Version                           
此题不会，翻了讨论区才勉强搞出来的朴素的深度优先解法：             
```c++            
class Solution {
public:
    vector<vector<int>> floodFill(vector<vector<int>>& image, int sr, int sc, int newColor) {
        if (image[sr][sc] == newColor) return image;
        
        int oldColor = image[sr][sc];
        if (image[sr][sc] != newColor){
            dfs(image, sr, sc, oldColor, newColor);
        }
        return image;
    }
    
    
private:
    void dfs(vector<vector<int>>& image, int sr, int sc, int oldColor, int newColor){
        if (sr < 0 || sr == image.size() || sc < 0 || sc == image[0].size() || image[sr][sc] != oldColor) return;
        image[sr][sc] = newColor;
        dfs(image, sr-1, sc, oldColor, newColor);
        dfs(image, sr+1, sc, oldColor, newColor);
        dfs(image, sr, sc-1, oldColor, newColor);
        dfs(image, sr, sc+1, oldColor, newColor);
    }
};
```                
Runtime: 8 ms, faster than 81.26% of C++ online submissions for Flood Fill.              
Memory Usage: 14 MB, less than 81.29% of C++ online submissions for Flood Fill.         

一个中规中矩的邻接矩阵图深度优先搜索，使用了辅助函数 dfs 进行递归。首先进行一次目标区域颜色是否是目标颜色的判断，是的话直接返回，节省一定时间。然后提取旧颜色，用于 dfs 递归（深度优先），递归结束，返回 image 。             

随后又根据 discuss 中的一个 [java 版本 BFS](https://leetcode.com/problems/flood-fill/discuss/473494/Java-DFS-BFS-Solutions-Space-complexity-Analysis-Clean-and-Concise) 广度优先解法搞了个 c++ 广度优先：              

```c++           
class Solution {
public:
    vector<vector<int>> floodFill(vector<vector<int>>& image, int sr, int sc, int newColor) {
        if (image[sr][sc] == newColor) return image;
        
        int oldColor = image[sr][sc];
        int Row = image.size(), Col = image[0].size();
        list<pair<int, int>> itemList;
        itemList.push_back( ( make_pair(sr, sc)));
        while( !itemList.empty()){
            pair<int, int> thisItem = itemList.front();
            itemList.pop_front();
            int thisRow = get<0>(thisItem), thisCol = get<1>(thisItem);
            if (thisRow < 0 || thisRow == Row || thisCol < 0 || 
                thisCol == Col || image[thisRow][thisCol] != oldColor)
                continue;
            image[thisRow][thisCol] = newColor;
            
            itemList.push_back( ( make_pair(thisRow-1, thisCol)));
            itemList.push_back( ( make_pair(thisRow+1, thisCol)));
            itemList.push_back( ( make_pair(thisRow, thisCol-1)));
            itemList.push_back( ( make_pair(thisRow, thisCol+1)));
        }
        return image;
    }
};
```             

Runtime: 4 ms, faster than **97.94%** of C++ online submissions for Flood Fill.           
Memory Usage: 14.2 MB, less than 29.53% of C++ online submissions for Flood Fill.         

广度优先的实现稍微复杂一点点，但思路似乎更简单：设计一个 List，将第一个元素加入到 List ，立刻进入 while 循环，循环终止条件为 List 置空。循环内部执行：      
提取并移除头部元素，判断头部元素是否符合 fill 的条件（边界条件及值条件），不满足则 continue 进入下一轮，满足则 fill 并将上下左右四个元素依次加入到 List 尾部。       

### 695. Max Area of Island             

You are given an m x n binary matrix grid. An island is a group of 1's (representing land) connected 4-directionally (horizontal or vertical.) You may assume all four edges of the grid are surrounded by water.           
The area of an island is the number of cells with a value 1 in the island.            
Return the maximum area of an island in grid. If there is no island, return 0.             

Example 1:             
Input: grid =           
[[0,0,1,0,0,0,0,1,0,0,0,0,0],            
 [0,0,0,0,0,0,0,1,1,1,0,0,0],           
 [0,1,1,0,1,0,0,0,0,0,0,0,0],          
 [0,1,0,0,1,1,0,0,1,0,1,0,0],          
 [0,1,0,0,1,1,0,0,1,1,1,0,0],           
 [0,0,0,0,0,0,0,0,0,0,1,0,0],            
 [0,0,0,0,0,0,0,1,1,1,0,0,0],                 
 [0,0,0,0,0,0,0,1,1,0,0,0,0]]            
Output: 6             
Explanation: The answer is not 11, because the island must be connected 4-directionally.             

Example 2:             
Input: grid = [[0,0,0,0,0,0,0,0]]             
Output: 0             

Constraints:           
* m == grid.length          
* n == grid[i].length            
* 1 <= m, n <= 50              
* grid[i][j] is either 0 or 1.

#### My AC Version            
有了上一题的经验，已经可以徒手撸中规中矩的 DFS 和 BFS 了。先上一个DFS。       
```c++          
class Solution {
public:
    int maxAreaOfIsland_dfs(vector<vector<int>>& grid) {
        int maxArea = 0;
        for (int i = 0; i < grid.size(); i ++){
            for (int j = 0; j < grid[0].size(); j++){
                if (grid[i][j]==1){
                    int thisArea = 0;
                    dfs(grid, i, j, thisArea);
                    maxArea = max(maxArea, thisArea);
                }
            }
        }
        return maxArea;     
    }

private:
    void dfs(vector<vector<int>>& grid, int i, int j, int& thisArea){
        if (i < 0 || i == grid.size() || j < 0 || j == grid[0].size() || 
            grid[i][j] != 1 ) return;
        thisArea ++;
        grid[i][j] = 2;
        dfs(grid, i-1, j, thisArea);
        dfs(grid, i+1, j, thisArea);
        dfs(grid, i, j-1, thisArea);
        dfs(grid, i, j+1, thisArea);
    }
};
```            
Runtime: 16 ms, faster than 84.46% of C++ online submissions for Max Area of Island.             
Memory Usage: 23.3 MB, less than 70.36% of C++ online submissions for Max Area of Island.           
时间空间表现还算可以。         
事实上，这个DFS with helper function 解法平平无奇，没有什么特色。需要注意的一点是，避免使用额外的二维 mask 标记数组。一来容易出错，也不比使用题目提供的 vector 本身作为标记数组（比如访问过的陆地值 1 置 0）方便快捷；二者，二维数组传参访问麻烦了些。         
再看一个 BFS 解法，跟上一题也几乎一致。           
```c++           
class Solution {
public:
    int maxAreaOfIsland(vector<vector<int>>& grid) {
        list<pair<int, int>> itemList;
        int maxArea = 0;
        for (int i = 0; i < grid.size(); i ++){
        for (int j = 0; j < grid[0].size(); j++){
            int thisArea = 0;
            if (grid[i][j] == 1)
                itemList.push_back(make_pair(i,j));
            while(!itemList.empty()){
                pair<int, int> thisItem = itemList.front();
                itemList.pop_front();
                int row = get<0>(thisItem), col = get<1>(thisItem);
                if (row < 0 || row == grid.size() || col < 0 || col == grid[0].size() || grid[row][col] != 1)
                    continue;
                thisArea ++;
                grid[row][col] = 0;
                itemList.push_back(make_pair(row-1, col));
                itemList.push_back(make_pair(row+1, col));
                itemList.push_back(make_pair(row, col-1));
                itemList.push_back(make_pair(row, col+1));
            }
            maxArea = max(thisArea, maxArea);
        }
        }
        return maxArea;
    }
};
```            
Runtime: 36 ms, faster than 20.78% of C++ online submissions for Max Area of Island.           
Memory Usage: 30.7 MB, less than 6.53% of C++ online submissions for Max Area of Island.               
速度和空间表现都不太好，随意吧。            
行，本题完美收工。           
