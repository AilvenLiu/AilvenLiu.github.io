---
layout:     post
title:      Series Article of cpp -- 31          
subtitle:   C++11 中的 lambda 表达式               
date:       2022-04-30
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:          
    - c++
---      

lambda 表达 c++ 中的可调用对象之一，在 C++11 中被引入到标准库中，使用时不需要包含任何头文件，但是编译时需要指定 `-std=c++11` 或其他支持 c++11 标准的编译命令（比如 `-std=c++0x` 或 `-std=c++14` 或 `-std=c++1y` )。lambda 表达式源于函数式编程的概念，简单来说它是一个匿名函数。它最大的作用就是不需要额外再写一个函数或者函数对象，避免了代码膨胀功能分散，让开发人员更加集中精力在手边的问题，提高生产效率。            

### 基本概念和用法           

c++ 中，普通的函数长这样：          
```c++
ret_value function_name(parameter) option { function_body; }       
```       

比如：        
```c++
int get_value(int a) const {return a++;}          
```      

lambda 表达式定义了一个匿名函数，并且可以捕获所定义的匿名函数外的变量。它的语法形式是：
```c++
[ capture ] ( parameter ) option -> return_type { body; };
```        
其中：       
* `capture` 捕获列表        
* `parameter` 参数列表        
* `option` 函数选项         
* `return_type` 函数返回值类型        
* `body` 函数体        

比如：
```c++
// defination           
auto lamb = [](int a) -> int { return a++; };

// usage         
std::cout << lamb(1) << std::endl;  // output: 2              
```

组成 lambda 的各部分并不都是必须存在：          
1. 当编译器可以推导出返回值类型的时候， 可以省略返回值类型部分：          
   ```c++
   auto lamb = [](int i) {return i;}; 	// OK, return tyoe is int         
   auto lamb2= []() {return {1,2};};	// Error.             
   ```

2. lambda 表达是没有参数的时候，参数列表可以省略           
   ```c++
   auto lamb = [](){return 1;};	// OK             
   ```
   所以一个最简单的 lambda 表达式可以是这样：         
   ```c++
   int main(){
	   []();
	   return ;
   }
   ```

### 捕获列表        
捕获列表是 lambda 表达式和普通函数区别最大的地方。 `[]` 内就是 lambda 表达式的捕获列表。一般来说，lmabda 表达式都是定义在其它函数内部，捕获列表的作用，就是使 lmabda 表达是内部的变量能够重用所有的外部变量。 捕获列表可以有如下的形式：        
* `[]` 不捕获任何变量       
* `[&]` 以引用的方式捕获外部作用域的所有变量           
* `[=]` 以赋值的方式捕获外部作用域的所有变量        
* `[x]` 以赋值的方式捕获外部变量 x ，不捕获其他变量           
* '[&x]' 以引用的方式捕获外部变量 x ，不捕获其他变量          
* `[=, &x]` 以引用的方式捕获外部变量 x ，以赋值的方式捕获其他变量        
* `[&, x]` 以赋值的方式捕获外部变量 x ，以引用的方式捕获其他变量        
* `[this]` 捕获当前类的 this 指针，使 lambda 表达式拥有和当前类成员同样的访问权限，可以**修改类的成员变量，使用类的成员函数**。如果已经使用了 `[=]` 或 `[&]`，则默认添加此项。        

给出示例如下：           
```c++
#include <iostream>

class TLambda{
public:
	TLambda(): i_(0){}
	int i_;

	void func(int x, int y){
		int a, b;

		// 错误，未捕获 this，无法访问 i_。正确示例见 l4                 
		auto l1 = []() {return i_;};

		// 正确，[=] 按值捕获所有外部变量，同时捕获 this           
		auto l2 = [=]() {return i_ + x + y;};

		// 正确，[&] 按引用捕获所有外部变量，同时捕获 this       
		auto l3 = [&]() {return i_ + x + y;};

		// l1 的正确实例         
		auto l4 = [this]() {return i_};

		// 错误，只捕获了 this，未捕获其他外部变量，无法使用，正确示例见 l6        
		auto l5 = [this]() {return x + y;};

		// 正确，等价于 l2           
		auto l6 = [this, x, y]() {return i_ + x + y;};

		// 正确          
		auto l7 = [this]() {return i_++;};

		// 错误，没有捕获外部变量 a            
		auto l8 = []() {return a;};

		// 正确，按引用捕获外部变量 b，按值捕获 a。           
		auto l9 = [a, &b]() {return a + ++b；};

		// 正确，按引用捕获外部变量 b，按值捕获其他变量。            
		auto l10 = [=, &b]() {return a + ++b;};
	}
}
```

