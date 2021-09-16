---
layout:     post
title:      Series Article of UbuntuOS -- 21         
subtitle:   linux 编译 opencv/C++                   
date:       2021-09-16      
author:     OUC_LiuX     
header-img: img/wallpic02.jpg     
catalog: true
tags:
    - Ubuntu OS
    - c++
---

相比起 opencv/python，c++ 写的 opencv 编译起来蛮麻烦的，本文给出 g++ 和 cmake 的两种编译方法。        

假设要编译以下一段代码：     
```c++     
// file name: opencv.cpp    

#include <iostream>
#include <opencv2/opencv.hpp>
 
using namespace std;
using namespace cv;
 
int main()
{
    Mat srcImage = imread("lena.jpg");
    imshow("源图像",srcImage);
 
    waitKey(0);
 
    return 0;
}
```     
**g++ 命令行方式：**        
```shell     
g++  opencv.cpp  -o opencv `pkg-config opencv --libs --cflags`
```       

相比于正常的 g++ 编译，只需要添加 `pkg-config opencv --cflags --libs` 参数即可。      

**CMAKE方式：**       

在项目（文件）同级目录下建立 `CMakeLists.txt` 文件，文件内容为：     
```txt      
cmake_minimum_required(VERSION 2.8)
project(opencv)
find_package(OpenCV REQUIRED)
add_executable(opencv opencv.cpp)
target_link_libraries(opencv ${OpenCV_LIBS})
```    
`project` 名字自己改，随意，跟后面匹配就成。然后：     
```she'll    
$ mkdir build && cd build
$ cmake ..
$ cd ..
$ make .
```      
完成编译。    


