---
layout:     post
title:      Series Article of cpp -- 07
subtitle:   使用 bitset 容器实现整型数和二进制数之间的转换        
date:       2021-10-07
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:     
    - c++     
    - STL      
---     
> 参考 [StackOverlow](https://stackoverflow.com/questions/19583720/convert-bitset-to-int-in-c) 和 《C++标准库（第二版）》电子工业出版社中译本 650 页， 12.5 节 Bitset。          
         
给出例程如下：           
```c++           
#include <iostream>
#include <bitset>
#include <string>
int main(){
	uint32_t n = 3452;
	std::bitset<32> bs(n);
	std::string sb = bs.to_string();
	printf("%s\n", sb.c_str());
	unsigned long long int ull;
	ull = bs.to_ullong(); 
    int in;
    in = (int) bs.to_ullong();
	printf("%lld\n", ull);
	return 0;
}
```          
**Output:**          
00000000000000000000110101111100             
3452           

**Explination:**              
`bitset<32> bs(n)` 可以将整型数 n 转换为有 32 bits 的二进制数 bs （数据类型为 bitset）；         
`bs.to_string()` 可以将存储在 bitset 容器中的二进制数 bs （内容不变）转换为 string ；       
`bs.to_ullong()` 可以讲存储在 bitset 容器中的二进制数 bs 转换为 无符号长长整型数；但注意如果将被复制的左操作数不是 unsigned long long，则需要一个强制类型转换以明确数据类型。        