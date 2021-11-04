---
layout:     post
title:      Series Article of cpp -- 19
subtitle:   memset只能赋值为0或-1          
date:       2021-11-02
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:     
    - c++   
---     

当我们运行下面的一段代码试图将数组中的数字全置为 1：         
```c++        
#include<bits/stdc++.h>
using namespace std;
int main() {
	int arr[5],b;
	memset(arr,1,sizeof(arr));
	for(auto i:arr)
		cout<<i<<" "; 
}
```         

会发现结果为：16843009 16843009 16843009 16843009 16843009           

这是因为 memset 是以字节为单位来完成赋值的，而一个int有四个字节，也就是说在上面的例子中其实我们赋的值是十六进制的 0x01010101 ，即二进制数 0000 0001 0000 0001 0000 0001 0000 0001，换算成十进制为 16843009，最终得出我们上面的结果。          

因此如果我们想将数组置为我们要的十进制值，只能置为0或-1，因为0的二进制表示全为0，-1的二进制表示全为1，按字节为单位完成赋值的结果可以保持不变。              
 