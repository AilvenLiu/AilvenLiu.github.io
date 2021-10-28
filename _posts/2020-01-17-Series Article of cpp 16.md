---
layout:     post
title:      Series Article of cpp -- 16
subtitle:   string 访问和添加元素          
date:       2021-10-28
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:     
    - c++   
    - STL
---     

string.at(idx), string[idx] 的返回值是 char 型；          
实测 string.append(char ch) 添加 char 型元素会出错，建议使用 += char 添加元素到当前字符串。           