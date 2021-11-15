---
layout:     post
title:      Series Article of cpp -- 24
subtitle:   vector 中的 resize 和 reverse            
date:       2021-11-15
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:     
    - c++   
    - STL
---     

### 区别      
reserve 是容器预留空间，但并不创建元素。在元素加入之前，不能引用容器内的元素。     
resize 是改变容器的大小，同时创建元素。因此，调用这个函数之后，就可以使用下标操作符 operator[] 或迭代器 iterator 操作容器内的元素了        
两个函数的形式也是有区别的。reserve 函数只有一个参数，即需要预留的容器的空间。resize函数可以有两个参数，第一个参数是容器新的大小，第二个参数是要加入容器中的新元素的初始值；如果这个参数被省略，那么就调用元素对象的默认构造函数。给出示例：     

### 实例        
```c++       
vector<int> myVec;
myVec.reserve( 100 );     // 新元素还没有构造,
                          // 此时不能用[]访问元素 
for (int i = 0; i < 100; i++ ){
     myVec.push_back( i ); //新元素这时才构造
}
myVec.resize( 102 );      // 用元素的默认构造函数构造了两个新的元素
myVec[100] = 1;           //直接操作新元素
myVec[101] = 2;
```
