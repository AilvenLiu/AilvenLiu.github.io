---
layout:     post
title:      Series Article of Algorithm and Data Structure -- 01 
subtitle:   差分     
date:       2022-03-1４
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Algorithm      
--- 

> from ACWing [797. 差分](https://www.acwing.com/problem/content/description/799/)           

### 差分基础       

差分是前缀和的逆操作。设有原数组 a[N] 和其差分数组 b[N]，则：　　　　　　
1. a[n] = a[n-1] + b[n];          
2. b[n] = a[n] - a[n-1];      

现有问题如下：　　　　　　　　

对数组 a[N] 进行 m 次操作，每次操作使 [l, r] 区间元素值加 c 。        
对该类问题，相比于直接操作原数组，使用差分方法解，可以将时间复杂度从 O(nm) 降低至 O(n+m)。             

以差分方法解，相当于：       
a 的差分数组元素 b[l] 值加 c，b[r+1] 值减 c，完成所有操作后通过 差分数组 b[N] 重构出原始数组 a[N] ，既是答案。          

### 原数组处理：         

```c++ 
#include<iostream>

using namespace std;

const int N = 100010;

int arr[N];


int main(){
    int n, m;
    cin >> n >> m;
    
    for (int i = 1; i <= n; i ++)
        scanf("%d", &arr[i]);
    
    while(m--){
        int l, r, c;
        scanf("%d %d %d", &l, &r, &c);
        for (int i = l; i <= r; i ++)
            arr[i] += c;
    }
    
    for (int i = 1; i <= n; i ++)
        printf("%d ", arr[i]);
    printf("\n");
    
    return 0;
}
```

复杂度 O(nm) ，较长数据报TLE。         

### 差分方法处理          

```c++        
#include<iostream>

using namespace std;

const int N = 100010;

int arr[N];
int b[N];


int main(){
    int n, m;
    cin >> n >> m;
    
    for (int i = 1; i <= n; i ++){
        scanf("%d", &arr[i]);
        b[i] = arr[i] - arr[i-1];
    }
    
    while(m--){
        int l, r, c;
        scanf("%d %d %d", &l, &r, &c);
        b[l] += c, b[r+1] -= c;
    }
    
    for (int i = 1; i <= n; i ++){
        arr[i] = arr[i-1] + b[i];
        printf("%d ", arr[i]);
    }
    printf("\n");
    
    return 0;
}
```   

复杂度 O(m+n) ，通过。