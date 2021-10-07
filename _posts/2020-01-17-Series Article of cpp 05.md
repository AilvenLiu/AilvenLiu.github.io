---
layout:     post
title:      Series Article of cpp -- 05
subtitle:   建议使用 emplace 代替 insert           
date:       2021-10-01
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:     
    - c++ 
---     

> 使用 insert 向荣其中加入元素的时候，有出错的可能。尤其是执行 `vec.insert(pos, vec[i])` , `vec.insert(pos, vector.begin()+x, vec.begin()+y)` 类似的从本容器中择出元素加入到本容器指定位置的时候，极容易出错。猜测是因为 insert 会生成一份拷贝，而从本容器中择出元素会涉及到地址拷贝，并不稳定。但 emplace(pos, elem) 则直接插入值为 elem 的元素，避免了拷贝操作，更稳定安全。            

C++11中大部分的容器对于添加元素除了传统的 insert 或者　pusb_back/push_front 之外都提供一个新的函数叫做　emplace。 比如如果你想要向 std::vector 的末尾添加一个数据，你可以：         
```c++
std::vector<int> nums;
nums.push_back(1);
```         
你也可以使用：        
```c++
std::vector<int> nums;
nums.empace_back(1);
```        

### 避免不必要的临时对象的产生           
emplace 最大的作用是避免产生不必要的临时变量，因为它可以完成 in place 的构造，举个例子：           
```c++          
struct Foo {
    Foo(int n, double x);
};

std::vector<Foo> v;
v.emplace(someIterator, 42, 3.1416);        // 没有临时变量产生           

v.insert(someIterator, Foo(42, 3.1416));    // 需要产生一个临时变量          

v.insert(someIterator, {42, 3.1416});       // 需要产生一个临时变量
```
这是 emplace 和 insert 最大的区别点。emplace 的语法看起来不可思议，在上 面的例子中后面两个参数自动用来构造 vector 内部的 Foo 对象。做到这一点主要 使用了 C++11 的两个新特性 变参模板 和 完美转发。“变参模板” 使得 emplace 可以接受任意参数，这样就可以适用于任意对象的构建。“完美转发”使得接收下来的参数 能够原样的传递给对象的构造函数，这带来另一个方便性就是即使是构造函数声明为 *explicit* 。它还是可以正常工作，因为它不存在临时变量和隐式转换。

```c++          
struct Bar {
    Bar(int a) {}
    explicit Bar(int a, double b) {}
};

int main(void)
{
    vector<Bar> bv;
    bv.push_back(1);        // 隐式转换生成临时变量           

    bv.push_back(Bar(1));   // 显示构造临时变量           

    bv.emplace_back(1);     // 没有临时变量            

    //bv.push_back({1, 2.0});   //  无法进行隐式转换        
    bv.push_back(Bar(1, 2.0));  //  显示构造临时变量          
    bv.emplace_back(1, 2.0);    //  没有临时变量        


    return 0;
}
```           
