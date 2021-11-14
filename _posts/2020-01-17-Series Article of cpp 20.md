---
layout:     post
title:      Series Article of cpp -- 20
subtitle:   unordered 无序容器和常规容器的区别           
date:       2021-11-04
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:     
    - c++   
    - STL
---     
> 以 set 为例说明 unordered 无序容器和常规容器的区别，map 也是一样的。       

### 底层数据结构       
1. set基于红黑树实现，红黑树具有自动排序的功能，因此map内部所有的数据，在任何时候，都是有序的。        
2. unordered_set基于哈希表，数据插入和查找的时间复杂度很低，几乎是常数时间，而代价是消耗比较多的内存，无自动排序功能。底层实现上，使用一个下标范围比较大的数组来存储元素，形成很多的桶，利用hash函数对key进行映射到不同区域进行保存。       

<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/cpp/cpp05.png"></div>       

###  set与unordered相比：     
1. set比unordered_set使用更少的内存来存储相同数量的元素。     
2. 对于少量的元素，在set中查找可能比在unordered_set中查找更快。        
3. 尽管许多操作在unordered_set的平均情况下更快，但通常需要保证set在最坏情况下有更好的复杂度(例如insert)。         
4. 如果您想按顺序访问元素，那么set对元素进行排序的功能是很有用的。       
5. 您可以用<、<=、>和>=从字典顺序上比较不同的set集。unordered_set集则不支持这些操作。        

### 在如下情况，适合使用set：       
1. 我们需要有序的数据(不同元素)。      
2. 我们必须打印/访问数据(按排序顺序)。        
3. 我们需要知道元素的前任/继承者。       

### 在如下情况，适合使用unordered_set：       
1. 我们需要保留一组元素，不需要排序。      
2. 我们需要单元素访问，即不需要遍历。        
3. 仅仅只是插入、删除、查找的话。         