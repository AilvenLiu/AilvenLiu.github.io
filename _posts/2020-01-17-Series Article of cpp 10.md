---
layout:     post
title:      Series Article of cpp -- 10
subtitle:   不建议使用\ <bits/stdc++.h>        
date:       2021-10-10
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:     
    - c++     
    - STL      
---     
> from [StackOverflow](https://stackoverflow.com/questions/31816095/why-should-i-not-include-bits-stdc-h) 。          
         
Including <bits/stdc++.h> appears to be an increasingly common thing to see on Stack Overflow, perhaps something newly added to a national curriculum in the current academic year.           

I imagine the advantages are vaguely given thus:           

* You only need write one #include line          
* You do not need to look up which standard header everything is in           

Unfortunately, this is a lazy hack, naming a GCC internal header directly instead of individual standard headers like <string>, <iostream> and <vector>. It ruins portability and fosters terrible habits.           

The disadvantages include:          
* It will probably only work on that compiler            
* You have no idea what it'll do when you use it, because its contents are not set by a standard            
* Even just upgrading your compiler to its own next version may break your program           
* Every single standard header must be parsed and compiled along with your source code, which is slow and results in a bulky executable under certain compilation settings

**Don't do it!**            
