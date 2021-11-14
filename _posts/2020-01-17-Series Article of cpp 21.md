---
layout:     post
title:      Series Article of cpp -- 21
subtitle:   引用，指针，智能指针           
date:       2021-11-14
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:     
    - c++   
---     
## 引用和指针        
### 区别       
**指针** 存放的是地址，指针可以被重新赋值，可以在初始化时指向一个对象，也可以不赋值即 pointer = nullptr；在其它时刻也可以指向另一个对象。指针在生命周期内随时都可能是空指针，所以在每次使用时都要做检查，防止出现空指针异常问题。     
**引用** 作为变量别名而存在，总是指向它最初代表的那个对象。引用恒不需要检查是否为空，因为引用永远都不会为空，它一定有本体，一定得代表某个对象，引用在创建的同时必须被初始化。      

### const 引用       
const引用让变量拥有只读属性。这个只读属性是针对当前的这个别名，变量是可以通过其它方式进行修改：          
```c++      
int a = 4;              
const int& b = a;     
int* p = (int*)&b;    // p = &a，取地址       
a = 6;     // ok，变量值改变，同时也有其常量引用 b = 5          
b = 5;     // 不 ok, 由于引用 b 是被 const 修饰的只读常亮，无法被赋值     
printf("a = %d\n", a);       // a = 6      
*p = 5;    // ok，通过地址（指针）赋值，必 ok 。     
printf("a = %d\n", a);       // a = 5        
```      

当使用常量对 const 引用进行初始化时，C++编译器会为常量值分配空间，并将引用名作为这段空间的别名：      
```c++   
const int& c = 1;
int* p = (int*)&c;   
c = 5;      // 不 ok，c 是被 const 修饰的只读常量。       
*p = 5;     // ok      
printf("c = %d\n", c);     // c = 5      
```
### 引用本质     
C++编译器在编译过程中用 **指针常量** 作为引用的内部实现，因此引用所占用的空间大小于指针相同。      


## 智能指针      
智能指针就是一个类，当超出了类的作用域是，类会自动调用析构函数，析构函数会自动释放资源，可以有效避免内存泄漏的问题。    
**智能指针定义于 memory (非memory.h)中**, 命名空间为 std 。      

### unique_ptr (c++11)         
用于取代c++98的auto_ptr的产物；虽然 auto_ptr 仍在 c++11 标准库中，但不应该继续去使用它。       
unique_ptr **独占所指向的对象**, 同一时刻只能有一个 unique_ptr 指向给定对象(通过禁止拷贝语义, 只有移动语义来实现), 定义于 memory (非memory.h)中, 命名空间为 std。       
1. unique_ptr 是禁止复制赋值的，始终保持一个 unique_ptr 管理一个对象；于是其无法在容器中使用，因为容器元素必须支持可复制可赋值。     
2. unique_ptr 虽然不能赋值，但可以通过 move() 函数转移对象的所有权。一旦被 move() 了，原来的 up1 则不再有效了（指空）。       
3. reset() 可以让 unique_ptr 提前释放指针。

```c++     
#include <iostream>
#include <memory>

class Test
{
public:
    void print()
    {
        std::cout << "Test::Print" << std::endl;
    }
};

int main()
{
    std::unique_ptr<Test> pTest1(new Test);
    pTest1->print();

    // std::unique_ptr<Test> pTest2(pTest1);      
    // 不 ok，禁止拷贝构造。        
	// std::unique_ptr<Test> pTest2 = pTest1;        
    // 不 ok，禁止赋值。      
	std::unique_ptr<Test> pTest2 = std::move(pTest1);
	// ok，通过 std::move() 转移所有权 
    pTest2->print();

    std::cout << "pTest1 pointer:" << pTest1.get() << std::endl;    
	//unique_ptr类的成员函数get()返回一个原始的指针         
    std::cout << "pTest2 pointer:" << pTest2.get() << std::endl;
    return 0;
}
// Print 输出        
// Test::Print      
// Test::Print      
// pTest1 pointer:0       
// pTest2 pointer:0x5646a6628e70      
```

### shared_ptr (c++11)           
基于引用计数模型。资源可以被多个指针共享，它使用计数机制来表明资源被几个指针共享。      
每次有 shared_ptr 对象指向资源，引用计数器就加1；当有 shared_ptr 对象析构时，计数器减1；当计数器值为0时，被指向的资源将会被释放掉（delete 操作符释放，或由用户提供的 删除器 释放它）。且该类型的指针可复制和可赋值，即其可用于STL容器中。         

```c++     
#include <iostream>
#include <memory>

class Test
{
public:
    void print()
    {
        std::cout << "Test::Print" << std::endl;
    }
};

int main()
{
    std::shared_ptr<Test> pTest1(new Test);
    pTest1->print();

    std::shared_ptr<Test> pTest2(pTest1);    // 拷贝构造函数        
    pTest2->print();

    std::cout << "pTest1 pointer:" << pTest1.get() << std::endl;    
    // shared_ptr类的成员函数get()返回一个原始的指针       
    std::cout << "pTest2 pointer:" << pTest2.get() << std::endl;

    std::cout << "count pTest1:" << pTest1.use_count() << std::endl;         
    // shared_ptr类的成员函数use_count()：返回多少个智能指针指向某个对象，
    // 主要用于调试。         
    std::cout << "count pTest2:" << pTest2.use_count() << std::endl;

    return 0;
}

// 打印结果：      
// Test::Print     
// Test::Print     
// pTest1 pointer:00C29550      
// pTest2 pointer:00C29550      
// count pTest1:2      
// count pTest2:2      
```    

**主要缺点：**        
shared_ptr相互引用,那么这两个指针的引用计数永远不可能下降为0，资源永远不会释放（死锁）。无法检测出循环引用，这会造成资源无法释放，从而导致内存泄露。 为了 fix 这个问题，C++11引入了另一个智能指针：weak_ptr         

### weak_ptr        
待补充，尚没有使用过。        