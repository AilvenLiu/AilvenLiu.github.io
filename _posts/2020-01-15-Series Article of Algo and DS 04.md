---
layout:     post
title:      Series Article of Algorithm and Data Structure -- 04 
subtitle:   单调栈和单调队列     
date:       2022-03-25
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Algorithm      
--- 

> from ACWing [830. 单调栈](https://www.acwing.com/problem/content/832/)， [154. 滑动窗口](https://www.acwing.com/problem/content/868/)。          

单调栈和单调队列，顾名思义，存储元素是单调的栈或队列。             
单调栈的典型应用场景：求数组某个数左边/右边离他最近的比它大或小的数。             
单调队列典型应用场景：滑动窗口求最值。              

## 单调栈             

有如下要求：            
> 给定一个长度为 N 的整数数列，输出每个数左边第一个比它小的数，如果不存在则输出 −1。         

则构造严格递增单调栈，数组元素从左到右遍历，while 栈不空且栈顶元素不小于当前元素，栈顶出栈。直到栈为空或栈顶元素小于当前元素，输出 -1 或栈顶元素，元素入栈。            

```c++
std::stack<int> s;

int main(){
    int n;  scanf("%d", &n);
    for (int i = 0; i < n; i ++){
        int x;              scanf("%d", &x);
        while(!s.empty() && s.top() >= x)   s.pop();
        if (s.empty())      printf("%d ", -1);
        else                printf("%d ", s.top());
        s.push(x);
    }
    return 0;
}
```


## 单调队列             

给出如下要求：             
> 给定一个大小为 n ≤ 10^6 的数组。         
> 有一个大小为 k 的滑动窗口，它从数组的最左边移动到最右边。            
> 你只能在窗口中看到 k 个数字。             
> 每次滑动窗口向右移动一个位置。             
> 你的任务是确定滑动窗口位于每个位置时，窗口中的最大值和最小值。           

由于题目要求输出最大最小值，则构造一次单增一次单减共两次单调队列。对于滑动窗口问题，入队列简单，条件符合即可以入，但出队列不好判断：需要记录元素下标。于是**队列不能直接存储元素值，应当存储元素在数组的下标**。               

输出窗口最小值，则构造单调递增队列：保证队列头是当前窗口最小值。           
while 队列不空且队尾大于等于当前值，队尾出队列直到队列为空或队尾元素小于当前值。当队列不空，判断队头是否已经移出窗口。当前元素（下标）入队列，返回队头元素。               

输出窗口最大值，则构造单调递减队列：保证队列头是当前窗口最大值。       
while 队列不空且队尾小于等于当前值，队尾出队列直到队列为空或队尾元素大于当前值。当队列不空，判断队列头是否已移出窗口。当前元素（下标）入队列，返回队头元素。        

给出题解：           

```c++
const int N = 1000010;
int arr[N];
deque<int> dq;

int main(){
    int n, k;   scanf("%d%d", &n, &k);

    // 求窗口最小值， 构造单调递增队列             
    for (int i = 0; i < n; i++){
        scanf("%d", arr[i]);
        
        while(!dq.empty() && arr[dq.back()] >= arr[i])  dq.pop_back();
        dq.push_back(i);
        if (dq.front() == i-k)  dq.pop_front(); // i-k，窗口前的 idx       
        if (i >= k-1)   printf("%d ", arr[front()]);    
        // 队列头就是当前窗口最小值的坐标               
    }

    dq.clear();
    puts();

    // 构造单调减队列求窗口最大值          
    for (int i = 0; i < n; i ++){
        while(!dq.empty() && arr[dq.back()] <= arr[i])  dq.pop_back();
        dq.push_back();
        if (dq.front() == i-k)  dq.pop_front();
        if (i >= k-1)   printf("%d ", arr[dq.front()]); 
        // 队列头存储窗口最大值坐标             
    }

    return 0;
}
```








