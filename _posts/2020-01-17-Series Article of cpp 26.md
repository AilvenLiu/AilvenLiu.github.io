---
layout:     post
title:      Series Article of cpp -- 26
subtitle:   how to convert from int to char*?            
date:       2021-11-24
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:     
    - c++   
    - STL
---     

> from [stackoverflow](https://stackoverflow.com/questions/10847237/how-to-convert-from-int-to-char)            

* In C++17, use std::to_chars as:
  ```c++ 
  std::array<char, 10> str;
  std::to_chars(str.data(), str.data() + str.size(), 42);
  ```     

* In C++11, use std::to_string as:
  ```c++         
  std::string s = std::to_string(number);        
  char const *pchar = s.c_str();  //use char const* as target type          
  ```         

* And in C++03, what you're doing is just fine, except use const as:       
  ```c++      
  char const* pchar = temp_str.c_str(); //dont use cast
  ```