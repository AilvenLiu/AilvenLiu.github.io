---
layout:     post
title:      Series Article of cpp -- 01
subtitle:   三目运算符*内部*完成赋值操作需要转 void        
date:       2021-09-25
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:     
    - c++ 
---     

刷 leetcode ，542 题 01 Matrix 需要在三目运算符内部完成一个赋值，形如：        
```c++    
aaa == bbb ? case 1 : case 2; 
```        
其中赋值操作出现在 case 语句中。这时候如果不做任何处理直接括号括起来，编译不通过，这是因为因为赋值操作是有返回值的，成功会返回一个 int 型的值，这个 int 值显然不能被 `aaa == bbb` 判断语句接收，所以需要强制转换成 void 如：        

```c++            
mat[i][j] == 0 ? q.push(make_pair(i, j)) : void( mat[i][j] = maxDis);       
```
