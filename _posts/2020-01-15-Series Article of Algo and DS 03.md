---
layout:     post
title:      Series Article of Algorithm and Data Structure -- 03 
subtitle:   静态链表     
date:       2022-03-23
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Algorithm      
--- 

> from ACWing [826. 单链表](https://www.acwing.com/problem/content/863/)， [827. 双链表](https://www.acwing.com/problem/content/864/)。          

静态链表增删改时间复杂度 O(1)， 查询需要遍历。可以用来存储树和图。       

## 静态单链表

静态单链表需要维护 值数组 `e[N]` 和 后向节点数组 `ne[N]` 两个静态数组。两个数组通过 idx 对应。idx 是全局变量，代表加入链表元素的顺序（从 1 开始），插入元素时 idx 增加，删除不减。后向节点存储的是 idx 。         
`idx = 0` 是头结点的位置，头结点没有值属性，只有后向节点 `ne[0]` 指向第一个元素。初始化为尾节点，也就是空节点，`ne[0]=-1` 作为链表结束判断标志。           

### 例题          

对于如下例题：          

> 第一行包含整数 M，表示操作次数。     
> 接下来 M 行，每行包含一个操作命令，操作命令可能为以下几种：          
> 1. `H x`，表示向链表头插入一个数 x。         
> 2. `D k`，表示删除第 k 个插入的数后面的数（当 k 为 0 时，表示删除头结点）。               
> 3. `I k x`，表示在第 k 个插入的数后面插入一个数 x（此操作中 k 均大于 0）。      
> 
> 将整个链表从头到尾输出。          

给出示例代码如下：           
```c++
#include<iostream>

using namespace std;

const int N = 100010;

int idx, e[N], ne[N];
char ch;

int main(){
    int n;  scanf("%d", &n);
    ne[0] = -1, idx = 1;
    
    while(n--){
        getchar();  scanf("%c", &ch);
        
        if (ch == 'H'){
            int x;  scanf("%d", &x);
            e[idx] = x;
            ne[idx] = ne[0];
            ne[0] = idx++;
        }
        else if (ch == 'I'){
            int k, x;   scanf("%d%d", &k, &x);
            e[idx] = x;
            ne[idx] = ne[k];
            ne[k] = idx++;
        }
        else if (ch == 'D'){
            int k;  scanf("%d", &k);
            ne[k] = ne[ne[k]];
        }
        
        else printf("error occured\n");
    }
    
    for(int i = ne[0]; i != -1; i = ne[i])
        printf("%d ", e[i]);
    
    return 0;
}
```

## 静态双链表        

静态双链表是单链表的扩展。需要维护三个静态数组：值数组、前向元素数组、后向元素数组。同样使用 idx 对应这三个数组。`idx = 0` 为头结点，`idx = N-1` 代表尾节点。正式操作元素从 `idx=1` 开始。          
前向节点和后向节点存储的是 idx 。头尾节点不具有值属性，头结点的前向节点和尾节点的后向节点恒为 -1 ，作为链表结束标志。初始化时，头结点的后向节点是尾节点，尾节点前向节点为头结点。       

### 例题       

对于如下例题：          
> 第一行包含整数 M，表示操作次数。            
> 接下来 M 行，每行包含一个操作命令，操作命令可能为以下几种：          
> 1. `L x`，表示在链表的最左端插入数 x。           
> 2. `R x`，表示在链表的最右端插入数 x。           
> 3. `D k`，表示将第 k 个插入的数删除。           
> 4. `IL k x`，表示在第 k 个插入的数左侧插入一个数。         
> 5. `IR k x`，表示在第 k 个插入的数右侧插入一个数。          
> 
> 将整个链表从左到右输出。            


给出示例代码如下：        
```c++
#include<iostream>

using namespace std;

const int N = 100010;

int idx, e[N], l[N], r[N];
string ch;

void insert(int k, int x){
    e[idx] = x;
    r[idx] = r[k];
    l[r[k]] = idx;
    l[idx] = k;
    r[k] = idx++;
}

void remove(int k){
    r[l[k]] = r[k];
    l[r[k]] = l[k];
}

int main(){
    l[0] = -1, r[N-1] = -1;
    r[0] = N-1, l[N-1] = 0;
    idx = 1;
    
    int n;  scanf("%d", &n);
    while(n--){
        cin >> ch;
        if ( ch == "L"){
            int x;  scanf("%d", &x);
            insert(0, x);
        }
        
        else if ( ch == "R"){
            int x;  scanf("%d", &x);
            insert(l[N-1], x);
        }
        
        else if ( ch == "D"){
            int k;  scanf("%d", &k);
            remove(k);
        }
        
        else if ( ch == "IL"){
            int k, x;   scanf("%d%d", &k, &x);
            insert(l[k], x);
        }
        
        else if ( ch == "IR"){
            int k, x;   scanf("%d%d", &k, &x);
            insert(k, x);
        }
        
        else printf("other case: %s\n", ch);
    
    }
    for (int i = r[0]; i != N-1; i = r[i])
        printf("%d ", e[i]);
    
    return 0;          
```









