---
layout:     post
title:      Series Article of cpp -- 22
subtitle:   多线程与锁           
date:       2021-11-15
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:     
    - c++   
---     

> 写在最前面：linux 下使用 g++/gcc 编译多线程程序，需要 `g++ -pthread` 参数。           

## 无锁         
考虑如下一段代码：            
```c++     
#include <iostream>
#include <thread>
#include <vector>
#include <chrono>

int counter = 0;
void increase(int time) {
    for (int i = 0; i < time; i++) {
        // std::this_thread::sleep_for(std::chrono::milliseconds(1));
        // 每次执行休眠一毫秒        
        counter++;
    }
}

int main(int argc, char** argv) {
    std::thread t1(increase, 10000);
    std::thread t2(increase, 10000);
    t1.join();
    t2.join();
    std::cout << "counter:" << counter << std::endl;
    return 0;
}
```        
理想地，程序运行的结果是 counter:20000。但实际情况是：       
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/cpp/cpp06.png"></div>       

出现上述情况的原因是：自增操作"counter++"不是原子操作，而是由多条汇编指令完成的。多个线程对同一个变量进行读写操作就会出现不可预期的操作。以上面的demo1作为例子：假定counter当前值为10，线程1读取到了10，线程2也读取到了10，分别执行自增操作，线程1和线程2分别将自增的结果写回counter，不管写入的顺序如何，counter都会是11。      

## 加锁         
定义一个 std::mutex 对象用于保护 counter 变量。对于任意一个线程，如果想访问 counter，首先要进行"加锁"操作，如果加锁成功，则进行 counter 的读写，读写操作完成后**释放锁**； 如果“加锁”不成功，则线程阻塞，直到加锁成功。

```c++     
#include <iostream>
#include <thread>
#include <vector>
#include <mutex>
#include <chrono>
#include <stdexcept>

int counter = 0;
std::mutex mtx; // 保护counter

void increase(int time) {
    for (int i = 0; i < time; i++) {
        mtx.lock();
        // 当前线程休眠1毫秒
        // std::this_thread::sleep_for(std::chrono::milliseconds(1));
        counter++;
        mtx.unlock();
    }
}

int main(int argc, char** argv) {
    std::thread t1(increase, 10000);
    std::thread t2(increase, 10000);
    t1.join();
    t2.join();
    std::cout << "counter:" << counter << std::endl;
    return 0;
}
```      
结果如下：       
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/cpp/cpp07.png"></div>       

这次运行结果和我们预想的一致，原因就是“利用锁来保护共享变量”，在这里共享变量就是counter（多个线程都能对其进行访问）。         

mutex 总结：       
1. 对于std::mutex对象，任意时刻最多允许一个线程对其进行上锁       
2. mtx.lock()：调用该函数的线程尝试加锁。如果上锁不成功，即：其它线程已经上锁且未释放，则当前线程block。如果上锁成功，则执行后面的操作，操作完成后要调用mtx.unlock()释放锁，否则会导致死锁的产生       
3. mtx.unlock()：释放锁      
4. std::mutex还有一个操作：mtx.try_lock()，字面意思就是：“尝试上锁”，与mtx.lock()的不同点在于：如果上锁不成功，当前线程不阻塞。        

## 死锁       
考虑这样一个情况：假设线程1上锁成功，线程2上锁等待。但是线程1上锁成功后，抛出异常并退出，没有来得及释放锁，导致线程2“永久的等待下去”，此时就发生了死锁。          

```c++     
#include <iostream>
#include <thread>
#include <vector>
#include <mutex>
#include <chrono>
#include <stdexcept>

int counter = 0;
std::mutex mtx; // 保护counter

void increase_proxy(int time, int id) {
    for (int i = 0; i < time; i++) {
        mtx.lock();
        // 线程1上锁成功后，立刻抛出异常。而锁未被释放。       
        if (id == 1) {
            throw std::runtime_error("throw excption....");
        }
        // 当前线程休眠1毫秒      
        // std::this_thread::sleep_for(std::chrono::milliseconds(1));      
        counter++;
        mtx.unlock();
    }
}

void increase(int time, int id) {
    try {
        increase_proxy(time, id);
    }
    catch (const std::exception& e){
        std::cout << "id:" << id << ", " << e.what() << std::endl;
    }
}

int main(int argc, char** argv) {
    std::thread t1(increase, 10000, 1);
    std::thread t2(increase, 10000, 2);
    t1.join();
    t2.join();
    std::cout << "counter:" << counter << std::endl;
    return 0;
}
```        
运行如下：      
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/cpp/cpp08.png"></div>       

发现发现程序却没有退出：发生了死锁。       

## 避免死锁          
std::lock_guard 是一个类，只有构造函数和析构函数。简单的来说：当调用构造函数时，会自动调用传入的对象的lock()函数，而当调用析构函数时，自动调用unlock()函数。      
```c++     
#include <iostream>
#include <thread>
#include <vector>
#include <mutex>
#include <chrono>
#include <stdexcept>

int counter = 0;
std::mutex mtx; // 保护counter

void increase_proxy(int time, int id) {
    for (int i = 0; i < time; i++) {
        // std::lock_guard对象构造时，自动调用mtx.lock()进行上锁      
        // std::lock_guard对象析构时，自动调用mtx.unlock()释放锁      
        std::lock_guard<std::mutex> lk(mtx);
        // 线程1上锁成功后，立刻抛出异常；且锁未被释放。      
        if (id == 1) {
            throw std::runtime_error("throw excption....");
            // 异常抛出，函数调用立刻结束，跳到异常捕获 catch 语句；
            // 当前函数栈结束，栈资源被释放，lk 析构调用，释放锁。     
        }
        counter++;
    }
}

void increase(int time, int id) {
    try {
        increase_proxy(time, id);
    }
    catch (const std::exception& e){
        std::cout << "id:" << id << ", " << e.what() << std::endl;
    }
}

int main(int argc, char** argv) {
    std::thread t1(increase, 10000, 1);
    std::thread t2(increase, 10000, 2);
    t1.join();
    t2.join();
    std::cout << "counter:" << counter << std::endl;
    return 0;
}
```      
结果如下：   
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/cpp/cpp09.png"></div>       
符合预期。       