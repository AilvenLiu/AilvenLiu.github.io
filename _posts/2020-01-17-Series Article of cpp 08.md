---
layout:     post
title:      Series Article of cpp -- 08
subtitle:   使用 printf() 打印字符串时建议 .c_str() 转为 C-String        
date:       2021-10-09
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:     
    - c++     
    - STL      
---     
> 参考 《C++标准库（第二版）》电子工业出版社中译本 669 页， 13.2.4 节 String 和 CString 。          
         
```c++       
std::string str1 = "test abc";
printf("%s", str1);
```          
上述代码大多数情况会报 error，即使勉强没报 error，也会报 warning .           
这是因为 printf 输出字符串是针对 char* 或 char[] 的（首字符地址），即 printf 只能输出 C 语言中的内置数据：           
```c++           
 char str1[] = "123456";
 const char* str2 = "test";
 printf("%s\n",str1);
 printf("%s", str2);
```          
而 string 不是 C 语言内置数据。          

根据C++标准库（第二版）》电子工业出版社中译本 66 页，可以使用 `.c_str()` 或 `.data()` 以字符数组形式返回 string 内容。该数组在 size() 位置上有一个 *end-of-string* 字符，所以其结果是一个含 `\0` 字符的有效的 C-String。       
请注意，`\0` 在 string 中并不具有特殊意义，和其他字符地位完全相同；但在一般的 C-String 中却用来标识结束。                   