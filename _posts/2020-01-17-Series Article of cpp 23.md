---
layout:     post
title:      Series Article of cpp -- 22
subtitle:   异常处理与函数调用           
date:       2021-11-15
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:     
    - c++   
---     

### 异常抛出于捕获       
C++ 通过 `throw` 语句和 `try...catch` 语句实现对异常的处理。`throw` 语句的语法如下：     
```c++      
throw 表达式;         
```      

该语句拋出一个异常。异常是一个表达式，其值的类型可以是基本类型，也可以是类。

`try...catch` 语句的语法如下：     
```c++
try {
    语句组
}
catch(异常类型) {
    异常处理代码
}
...
catch(异常类型) {
    异常处理代码
}
```     

catch 可以有多个，但至少要有一个。       
不妨把 try 和其后{}中的内容称作“try块”，把 catch 和其后{}中的内容称作“catch块”。         

`try...catch` 语句的执行过程是：        
执行 try 块中的语句，如果执行的过程中没有异常拋出，那么执行完后就执行最后一个 catch 块后面的语句，所有 catch 块中的语句都不会被执行；        
如果 try 块执行的过程中拋出了异常，那么拋出异常后立即跳转到第一个“异常类型”和拋出的异常类型匹配的 catch 块中执行（称作异常被该 catch 块“捕获”），执行完后再跳转到最后一个 catch 块后面继续执行。       

### 案例        
```c++       
#include <iostream>
using namespace std;
int main()
{
    double m ,n;
    cin >> m >> n;
    try {
        cout << "before dividing." << endl;
        if( n == 0)
            throw -1; //抛出int类型异常
        else
            cout << m / n << endl;
        cout << "after dividing." << endl;
    }
    catch(double d) {
        cout << "catch(double) " << d <<  endl;
    }
    catch(int e) {
        cout << "catch(int) " << e << endl;
    }
    cout << "finished" << endl;
    return 0;
}
```        
对上述程序输入 9 6         
有：       
```
before dividing.        
1.5        
after dividing.       
finished        
```
说明当 n 不为 0 时，try 块中不会拋出异常。因此程序在 try 块正常执行完后，越过所有的 catch 块继续执行，catch 块一个也不会执行。         

若输入 9 0          
有：        
```c++       
before dividing.
catch\(int) -1
finished
```
可知当 n 为 0 时，try 块中会拋出一个整型异常。拋出异常后，try 块立即停止执行。该整型异常会被类型匹配的第一个 catch 块捕获，即进入catch(int e)块执行，该 catch 块执行完毕后，程序继续往后执行，直到正常结束。         

如果拋出的异常没有被 catch 块捕获，例如，将catch(int e)，改为catch(char e)，当输入的 n 为 0 时，拋出的整型异常就没有 catch 块能捕获，这个异常也就得不到处理，那么程序就会立即中止，try...catch 后面的内容都不会被执行。          

如果希望不论拋出哪种类型的异常都能捕获，则 `catch` 语句应写为在 `catch(...){表达式;}`      


### 异常抛出后函数过程         
throw 抛出异常，try 块内语句执行立刻终止。若 throw 写在其他函数内，则函数立刻终止，当前函数栈立刻结束，栈内一切资源被释放；若函数内有类被实例化，析构函数自动调用，资源释放。         
