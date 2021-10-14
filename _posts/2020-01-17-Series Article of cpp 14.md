---
layout:     post
title:      Series Article of cpp -- 14
subtitle:   std::exchange         
date:       2021-10-13
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:     
    - c++   
---     

`std::exchange` 定义于 `<utility>`。            
* C++14 到 C++20 前定义为：        
  ```c++          
  template< class T, class U = T >        
  T exchange( T& obj, U&& new_value );      
  ```        
* C++20 定义为：        
  ```c++         
  template< class T, class U = T >          
  constexpr T exchange( T& obj, U&& new_value );       
  ```       

**功能：** 使用new_value 替换 obj 的值，并返回 obj 的旧值。（右边替换左边，返回左边的初值）        
**要求：** T 必须满足可移动构造 (MoveConstructible) 的要求。而且必须能移动赋值 U 类型对象给 T 类型对象。     