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

> from ACWing [797. 差分](https://www.acwing.com/problem/content/description/799/)， [798. 差分矩阵](https://www.acwing.com/activity/content/problem/content/832/)           

### 差分基础       

差分是前缀和的逆操作。设有原数组 a[N] 和其差分数组 b[N]，则：　　　　　　
1. `a[n] = a[n-1] + b[n];`          
2. `b[n] = a[n] - a[n-1];`      

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

### 二维矩阵情况            

同一维数组类似，二维差分是二维前缀和的逆操作， 二维矩阵是其差分矩阵的前缀和矩阵。     
对二维矩阵 a[N][M] 和其差分矩阵 b[N][M] ，有以下性质：       
1. `a[i][j] = a[i][j-1] + a[i-1][j] - a[i-1][j-1] + b[i][j];`                 
2. `b[i][j] = a[i][j] - a[i][j-1] - a[i-1][j] + a[i-1][j-1];`         

同样的，对于以下类型题目：           
给定矩阵 a[N][M] ，并进行 q 次操作，每次操作将[x1y1, x2y2] 范围元素值加 c。      
若使用原矩阵操作，时间复杂度为 O(mnq)，而使用差分矩阵 b[N][M] 操作相当于：          
```
b[x1][y1] += c，       
b[x1][y2+1] -= c,      
b[x2+1][y1] -= c,          
b[x2+1][y2+1] += c；        
```

时间复杂度可降为 O(q+mn) 。           

给出差分矩阵解：         

```c++
#include<iostream>

using namespace std;

const int N = 1010;
int a[N][N], b[N][N];

int main(){
    int n, m, q; 
    scanf("%d%d%d", &n, &m, &q);
    
    for (int i = 1; i <= n; i++){
        for (int j = 1; j <= m; j++){
            scanf("%d", &a[i][j]);
            b[i][j] = a[i][j] + a[i-1][j-1] - a[i-1][j] - a[i][j-1];
        }
    }
    
    while(q--){
        int x1, y1, x2, y2, c;
        scanf("%d%d%d%d%d", &x1, &y1, &x2, &y2, &c);
        
        b[x1][y1] += c, b[x2+1][y2+1] += c, b[x1][y2+1] -= c, b[x2+1][y1] -= c;
    }
    
    for (int i = 1; i <= n; i++){
        for (int j = 1; j <= m; j++){
            a[i][j] = a[i][j-1] + a[i-1][j] - a[i-1][j-1] + b[i][j];
        }
    }
    
    for (int i = 1; i <= n; i++){
        for (int j = 1; j <= m; j++){
            printf("%d ", a[i][j]);
        }
        printf("\n");
    }
    // printf("\n");
    
    return 0;
}
```     

