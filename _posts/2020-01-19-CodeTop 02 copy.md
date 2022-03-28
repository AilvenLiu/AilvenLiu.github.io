---
layout:     post
title:      Code Top -- 03 LRU缓存机制 
subtitle:   LRU缓存机制    
date:       2022-03-28
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Algorithm      
--- 

> from CodeTop, Leetcode, [146. LRU缓存](https://leetcode-cn.com/problems/lru-cache/)                       
> 面试高频。          

请你设计并实现一个满足  LRU (最近最少使用) 缓存 约束的数据结构。
实现 `LRUCache` 类：              
* `LRUCache(int capacity)` 以 正整数 作为容量 `capacity` 初始化 LRU 缓存                
* `int get(int key)` 如果关键字 key 存在于缓存中，则返回关键字的值，否则返回 -1 。              
* `void put(int key, int value)` 如果关键字 key 已经存在，则变更其数据值 value ；如果不存在，则向缓存中插入该组 key-value 。如果插入操作导致关键字数量超过 capacity ，则应该 逐出 最久未使用的关键字。            

函数 get 和 put 必须以 O(1) 的平均时间复杂度运行。          

## 面试高频，直接记思路                
时间戳机制使用 **双向链表** 实现，key-value 使用哈希表实现。            
双向链表的作用是，每操作一次节点，将被操作的节点拿出来放到链表头；这样，链表尾端节点一定是最早被操作的。         
还要维护一个哈希表 `unordered_map<key, Node*>`，哈希表的 value 是链表Node* 节点，以此实现 O(1) 复杂度的 get 和 put 。        
put 新节点（key 没有在 hasp_map 中出现过）的时候，先检查 hash 存储的节点数量，如果已经达到 cap ，则删掉最后链表一个节点。 hash_map 和 链表都要删。         

注意，链表节点也要有 key 属性，这是因为在 hash_map 中删除节点的时候我们不知道该节点的 key 是多少，需要从节点中读取。             

```c++
struct Node{
    int key, val;
    Node *left, *right;
    Node(int _key, _val): key(_key), val(_val), left(nullptr), right(nullptr) {};
    Node(): key(-1), val(-1), left(nullptr), right(nullptr) {};
} *L, *R;

int cap;
unordered_map<int, Node*> hash;

void remove(Node* p){
    p -> left -> right = p -> right;
    p -> right -> left = p -> left;
}

void insert(Node* p){
    p -> right = L -> right;
    p -> left = L;
    L -> right -> left = p;
    L -> right = p; 
}

LURCache(int capacity){
    cap = capacity;
    L = new Node();
    R = new Node();
    L -> right = R;
    R -> left = L;
}

int get(int key){
    if (!hash.count(key))   return -1;
    auto p = hash[key];
    remove(p);
    insert(p);
    return p -> val;
}

void put(int key, int value){
    if (hash.count(key)){
        auto p = hash[key];
        p -> val = value;
        remove(p);
        insert(p);
    }
    else {
        if (hase.size() == cap){
            hash.earse(R -> left -> key);
            remove(R -> left);
        }
        Node* p = new Node(key, value);
        insert(p);
        hash[key] = p;
    }
}
```