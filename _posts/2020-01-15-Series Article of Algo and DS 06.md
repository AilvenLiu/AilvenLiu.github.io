---
layout:     post
title:      Series Article of Algorithm and Data Structure -- 06 
subtitle:   堆排序    
date:       2022-03-27
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Algorithm      
--- 

> from ACWing , [838. 堆排序](https://www.acwing.com/problem/content/840/)          


堆是完全二叉树结构。         
* 堆存储：维护一个 cnt 变量存储堆元素数量，则可以使用一维静态数组 heap[N] 存储堆。堆顶下标为 1，idx 下标节点的左右儿子节点分别 `2*idx` 和 `2*idx+1`。        
* 上移：上移节点 k，则将之与 k/2 比较（若存在），若小于，则交换。并递归。          
* 下移：下移节点 K，则将之与 `u*2`, `u*2+1`两个子节点比较（若存在），若子节点小于当前节点，选择更小的子节点，交换，递归。
* 堆建立：从最后一个有子节点的元素，也就是 `int i = cnt/2` 开始向前，执行 down(i) 操作，保证每个节点都是以其为根的小堆的最小值。           
* 删除头部节点：尾节点覆盖头结点，cnt--，down(1)。           
* 删除任意节点：尾节点覆盖该节点，cnt--, down(k), up(k)。只有一个操作会被执行。         
* 插入新节点：插入到堆尾，cnt++，up(cnt)。          
* 改变指定节点值：改变，然后对该节点 down(k), up(k)。只有一个操作会被执行。        

### 节点上移下移              
以小根堆，或者叫升序堆为例。        
```c++
void down(int u){
    int t = u;
    if (2*u <= cnt && heap[2*u] < heap[t])      t = 2*u;
    if (2*u+1 <= cnt && heap[2*u+1] < heap[t])  t = 2*u+1;
    if (t > u){
        swap(heap[t, u]);
        down(t);
    }
}

void up(int u){
    if (u/2 && heap[u/2] > heap[u]){
        swap(heap[u], heap[u/2]);
        up(heap[u/2]);
    }
}
```

### 堆操作         

* 给定数组建堆           
  ```c++
  for (int i = n/2； i ; i--) down(i);
  ```

* 删除最小值（堆顶元素）          
  ```c++
  heap[1] = heap[cnt--];
  down(1);
  ```
  
* 删除第 k 个元素           
  ```c++
  heap[k] = heap[cnt--];
  down(k);
  up(k);
  ```

* 插入一个元素
  ```c++
  heap[++cnt] = x;
  up(cnt);
  ```

* 改变第 k 个元素值         
  ```c++
  heap[k] = x;
  up(k);
  down(k);
  ```