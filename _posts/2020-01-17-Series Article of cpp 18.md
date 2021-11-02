---
layout:     post
title:      Series Article of cpp -- 18
subtitle:   C++参数传递，数组引用传递，保护数组退化为指针          
date:       2021-10-28
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:     
    - c++   
---     

在进行参数的传递时，数组引用可以帮助我们防止数组退化为指针，而这是我们在编程中很难注意到的问题。             
下面来看一个实例：          
```c++         
#include <iostream>
void each(int int_ref[10])
{
      std::cout << sizeof(int_ref)<<std::endl;
      for(int i=0;i<10;i++)
            std::cout<<int_ref[i]<<" ";
       std::cout<<std::endl;
}

void each2(int (&int_ref)[10])
{
        std::cout<<sizeof(int_ref)<<std::endl;
        for(int i=0;i<10;i++)
               std::cout<<int_ref[i]<<" ";
       std::cout<<std::endl;
}
int main()
{
        int int_array[] = {1,2,3,4,5,6,7,8,9,10};
        each(int_array);//问题1：sizeof()的值？              

         each2(int_array);//问题2：sizeof()的值？              

         return 0;
}
```         
当然，如果不去理会sizeof（）的话，这两个函数的输出不会有任何的不同，他们都能够正确的输出array[]中的10个值，但当我们观察一下sizeof()的值就会发现很大的不同。            
问题1：输出4           
问题2：输出40           
从这我们就能看出，当array[]作为参数传递过去后，如果接收的参数是也是一个数组，那么它就会退化为一个指针，也就是我们常说的“数组就是一个指针”。当接收的参数是一个数组引用是，就会发现它还是保持了自己的原生态，即“数组仍然是一个数组”。这时，数组引用就起到了一个保护自己退化为一个指针的作用。           