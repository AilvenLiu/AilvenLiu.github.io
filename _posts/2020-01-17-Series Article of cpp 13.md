---
layout:     post
title:      Series Article of cpp -- 13
subtitle:   双端队列 deque         
date:       2021-10-13
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:     
    - c++     
    - STL
---     

> From [CSDN: C++ deque 底层原理及 queue、stack 容器的使用详解](https://blog.csdn.net/qq_38289815/article/details/106144793)                 

## 简介         

双端队列 (double-ended queue，缩写为deque)是一个容量可以动态变化，并且可以在两端插入或删除的顺序容器。既然它是顺序容器，那么容器中的元素就是按照严格的线性顺序排序，可以使用类似数组的方法访问对应的元素。deque 实际分配的内存数超过容纳当前所有有效元素的数量，因为空闲的空间会在增加元素的时候被利用。双端队列提供了类似 vector 的功能，不仅可以在容器末尾，还可以在容器开头高效地插入或删除元素。        

### 底层结构         

这里需要简单的了解 deque 的底层结构，从而更好的理解和使用 deque 。deque 是由一段一段的定量连续空间构成。一旦有必要在 deque 的前端或尾端增加新空间，便配置一段定量连续空间，串接在整个 deque 的头端或尾端。deque 的最大任务，便是在这些分段的定量连续空间上，维护其整体连续的假象，并提供随机存取的接口。避开了“重新配置、复制、释放”的轮回，代价则是复杂的迭代器结构。           
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/cpp/cpp02.png"></div>   

deque 采用一块所谓的 map (不是STL的map容器) 作为主控。map 是一小块连续空间，其中每个元素 (此处称为一个节点，node)都是指针，指向另一段(较大的)连续线性空间，称为缓冲区，而缓冲区才是deque的储存空间主体。              
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/cpp/cpp03.png"></div>   

deque 和 vector 的不同点从上述图片就可以体现出来，vector 的存储空间是连续的，而 deque 的存储空间是由若干个连续空间组成的。deque 巧妙地**引入了 map 作为主控，让使用者以为使用的空间是完全连续的**。这就好比我们使用的笔记本一样，我们不需要为了做笔记而买一个几百页的笔记本，只需要准备若干个小笔记本即可。当一个笔记本使用完毕后，开始使用下一个笔记本。此时，只要对笔记本进行编号就可以正确无误的使用它们了。比如，看完了第二本笔记本的内容，那么还需要继续往下看，那么理所当然的去使用第三本笔记本。          
deque 的分段连续空间，维持其"整体连续"假象的这一艰巨的任务，落在了迭代器的 operator++ 和 operator-- 两个运算符重载身上。仔细琢磨上图就很容易理解了。还是以笔记本为例，当看到第二本的末尾时，还需要查看后面的内容，那么就需要找到第三本笔记本。或者是看某一本笔记本时，突然要查阅基础知识 (往前查看内容)。完成这两个操作最重要的就是要知道笔记本的编号。在 deque 中由 map 主控完成这一任务，因为它的存在，迭代器才能找到某一连续空间的前一段或后一段的地址，而这一过程是较为复杂的。deque 的中控器、缓冲区、迭代器的相互关系如下：        
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/cpp/cpp04.png"></div>     

### 适配器概念             
适配器(配接器)在STL组件中，扮演者轴承、转换器的角色。adapter这个概念，事实上是一种设计模式。定义如下：将一个class 的接口转换为另一个 class 的接口，使原本因接口不兼容而不能合作的 class，可以一起运作。其中改变容器接口的，我们称之为容器适配器(container adapter)。          
> 源自：《STL源码剖析》          

下面的 queue 和 stack，其实是一种适配器。它们修饰 deque 的接口而成就出另一种容器风貌。由于 deque 两端都可以完成插入和删除操作，那么如果限制只能在一端完成插入和删除，这样便提供了栈的功能。如果限制只能在一端只能插入，而另一端只能删除，便提供了队列的功能。             

* `std::stack` 类是容器适配器，它给予程序员栈的功能： FILO （先进后出）数据结构。          
* `std::queue` 类是容器适配器，它给予程序员队列的功能—— FIFO （先进先出）数据结构。         

