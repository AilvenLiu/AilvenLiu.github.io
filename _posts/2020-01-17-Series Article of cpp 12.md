---
layout:     post
title:      Series Article of cpp -- 12
subtitle:   Whats the most efficient way to erase duplicates and sort a vector        
date:       2021-10-11
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:     
    - c++     
    - STL
---     

> From [StackOverflow](https://stackoverflow.com/questions/1041620/whats-the-most-efficient-way-to-erase-duplicates-and-sort-a-vector)                 

Let's compare three approaches:

1. Just using vector, sort + unique
```c++
sort( vec.begin(), vec.end() );
vec.erase( unique( vec.begin(), vec.end() ), vec.end() );
```          

2. Convert to set (manually)
```c++
set<int> s;
unsigned size = vec.size();
for( unsigned i = 0; i < size; ++i ) s.insert( vec[i] );
vec.assign( s.begin(), s.end() );
```           

3. Convert to set (using a constructor)
```c++
set<int> s( vec.begin(), vec.end() );
vec.assign( s.begin(), s.end() );
```           

Here's how these perform as the number of duplicates changes:          
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/cpp/cpp01.png"></div>    