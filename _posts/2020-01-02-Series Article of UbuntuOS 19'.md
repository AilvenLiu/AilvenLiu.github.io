---
layout:     post
title:      Series Article of UbuntuOS -- 19         
subtitle:   linux g++开启C++11/14/17/20支持                   
date:       2021-09-16      
author:     OUC_LiuX     
header-img: img/wallpic02.jpg     
catalog: true
tags:
    - Ubuntu OS
---

可以使用 `g++ -std=c++17` 类似的参数指定使用 c++17 标准编译。     
方便起见，也可以在 `~/.bashrc` 配置文件中指定别名（alias）如:       
```bash  
# in ~/.bashrc          

alias g++11='g++ -std=c++11'       
alias g++14='g++ -std=c++14'       
alias g++17='g++ -std=c++17'       
alias g++20='g++ -std=c++20'       
```        

如此，即可以使用 `g++20 test.cpp -o test` 类似的命令指定 c++20 标准编译文件了。     
