---
layout:     post
title:      Series Article of cpp -- 27          
subtitle:   CMake 基本使用               
date:       2022-04-20
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:          
    - WorkFlow
    - c++
---      

由于我的 workflow 是从 github 上面 down 下来，`mkdir build` 后自行 `cmake .. && make` 编译的，既不是通过 apt 安装，也没有执行 `make install`，编译完的源码没有加入到 `/usr/local/include` 等系统路径，于是写自己的 workflow C++ 程序的时候没有办法直接 `#include <workflow/WFxxx.h>` 来引入相关包，只能通过 g++ 参数或 cmake 方法指定头文件和链接文件。      

一份比较简洁清楚的 [cmake讲解](https://zhuanlan.zhihu.com/p/54253350) ：

下面先给出我的 `CMakeLists.txt` 文件，然后逐行解释语法和功能。             

**代码：**             
```cmake    
cmake_minimum_required(VERSION 3.6)

set(CMAKE_BUILD_TYPE RelWithDebInfo)

project(helloworld
		LANGUAGES C CXX
)

# set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR})         

find_library(LIBRT rt)
message(STATUS "LIBRT = ${LIBRT}") # 打印信息       

find_package(OpenSSL REQUIRED)

# find_package(workflow REQUIRED CONFIG HINTS ..)           
set(WORKFLOW_ROOT /home/ailven//data/cppProj/workflow)
set(WORKFLOW_INCLUDE_DIR ${WORKFLOW_ROOT}/_include)
set(WORKFLOW_LIB_DIR ${WORKFLOW_ROOT}/_lib)
set(workflow WORKFLOW_INCLUDE_DIR WORKFLOW_LIB_DIR)

include_directories(${OPENSSL_INCLUDE_DIR} ${WORKFLOW_INCLUDE_DIR})
link_directories(${WORKFLOW_LIB_DIR})

if (KAFKA STREQUAL "y")
	find_path(SNAPPY_INCLUDE_PATH NAMES snappy.h)
	include_directories(${SNAPPY_INCLUDE_PATH})
endif ()

set(CMAKE_C_FLAGS   "${CMAKE_C_FLAGS}   -Wall -fPIC -pipe -std=gnu90")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -fPIC -pipe -std=c++11 -fno-exceptions")


set(WORKFLOW_LIB workflow pthread OpenSSL::SSL OpenSSL::Crypto ${LIBRT})

aux_source_directory(. SRC_LISTS)
add_executable(${PROJECT_NAME} ${SRC_LISTS})
target_link_libraries(${PROJECT_NAME} ${WORKFLOW_LIB})
```

**逐行解释：**             

```cmake       
cmake_minimum_required(VERSION 3.6)
```       
指定 cmake 最低版本，如果低于这个版本，直接报错退出。           


```cmake 
set(CMAKE_BUILD_TYPE RelWithDebInfo)
```        
指定编译类别/级别（`CMAKE_BUILD_TYPE`）为 `RelWithDebInfo`。CMake 具有以下四种编译级别：           
1. `Release`： 不可以打断点调试，程序开发完成后发行使用的版本，占的体积小。 它对代码做了优化，因此速度会非常快，在编译器中使用命令： `-O3 -DNDEBUG` 可选择此版本。        
2. `Debug`： 调试的版本，体积大。在编译器中使用命令： `-g` 可选择此版本。           
3. `MinSizeRel`： 最小体积版本，在编译器中使用命令：`-Os -DNDEBUG` 可选择此版本。       
4. `RelWithDebInfo`： 既优化又能调试。在编译器中使用命令：`-O2 -g -DNDEBUG` 可选择此版本。          


```cmake 
project(helloworld
		LANGUAGES C CXX
)
```
指定项目名称为 helloworld，同时生成一个变量 `PROJECT_NAME` 存储这个名字。       
指定编译语言为 C/C++。         


```cmake 
find_library(LIBRT rt)
```
在三个系统默认路径中寻找第三方链接文件（.so, .a）（可以认为是头文件 .h 对应的源文件 .cpp）路径，这里是寻找 `librt.so`，并将路径赋值给 LIBRT 。      
三个默认路径是：         
1. `/usr/lib`        
2. `/usr/local/lib`        
3. `/usr/lib/x86_64-linux-gnu`         

`librt.so` 主要是 glibc 对 real-time 部分的支持。所以一般含有 `#include<time.h>` 头文件的代码，编译的时候需要该动态链接。        


```cmake 
find_package(OpenSSL REQUIRED)
```
寻找 OpenSSL 包，后面参数 REQUIRED 代表必须要找到，找不到则报错退出。        
找到以后，该包的头文件路径赋值给 `OpenSSL_INCLUDE_DIRS`，链接库文件赋值给 `OpenSSL_LIBRARIES`。对于 OPENCV 而言，库文件路径赋值给了 `OPENCV_LIBS`，什么情况下变量名为 `_LIBS` ，什么情况下为 `_LIBRARIES`，没有深入研究。           


```cmake
# find_package(workflow REQUIRED CONFIG HINTS ..)           
set(WORKFLOW_ROOT /home/ailven//data/cppProj/workflow)
set(WORKFLOW_INCLUDE_DIR ${WORKFLOW_ROOT}/_include)
set(WORKFLOW_LIB_DIR ${WORKFLOW_ROOT}/_lib)
set(workflow WORKFLOW_INCLUDE_DIR WORKFLOW_LIB_DIR)
```      
寻找 workflow 包，但是指定路径和默认路径都找不到，应该是我编译的时候没有 `make install` 的问题，于是注释掉 find_package 一行，通过 set 手动指定路径 。       
`set` 是一个通用的赋值命令，`set(VAR PATH [PATH2] [PATH3])` 可以将 VAR 后面的值赋给 VAR，如果有超过一个值，set 里面空格隔开，反映到 VAR 上面，是分号隔开的多个值。如果要单独使用，可以使用 `foreach` 语句。        


```cmake
include_directories(${OPENSSL_INCLUDE_DIR} ${WORKFLOW_INCLUDE_DIR})
link_directories(${WORKFLOW_LIB_DIR})
```
声明头文件和库文件目录（**注意，是文件夹的路径，不是具体文件的路径**），告诉 cmake 要在哪里寻找头文件和库文件。前者相当于 g++ 中的 `-I` 选项，后者相当于 `-L` 选项。都可以传入多个值。       
个人理解：这里只是指明头文件和库文件的路径，真正使项目能够通过编译，还要配合 `target_link_libraries(target links)` 语句将这些库文件链接到项目。        


```cmake
if (KAFKA STREQUAL "y")
	find_path(SNAPPY_INCLUDE_PATH NAMES snappy.h)
	include_directories(${SNAPPY_INCLUDE_PATH})
endif ()
```
使用卡夫卡时候需要引入的头文件，不管。            


```cmake
set(CMAKE_C_FLAGS   "${CMAKE_C_FLAGS}   -Wall -fPIC -pipe -std=gnu90")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -fPIC -pipe -std=c++11 -fno-exceptions")
```
gcc/g++ 编译时的参数，`-Wall` 表示开启所有警告； `-fPIC` 作用于编译阶段，告诉编译器产生与位置无关代码，也即，项目里使用了动态链接库 .so 文件是需要加入该参数； `-pipe` 表示使用管道代替编译中临时文件，可以加快编译速度； `-std=c++11` 容易理解，使用 `c++11` 标准； `-fno-exceptions` 表示禁用异常机制，不是很理解，不用管了。          


```cmake
set(WORKFLOW_LIB workflow pthread OpenSSL::SSL OpenSSL::Crypto ${LIBRT})
```
整合一下库文件和连接文件到一个变量。           


```cmake 
aux_source_directory(. SRC_LISTS)
add_executable(${PROJECT_NAME} ${SRC_LISTS})
```
检索当前路径下所有源文件(.c, .cc, .cpp)，赋值给 SRC_LISTS 变量。        
使用 SRC_LISTS 中的源文件生成以 PROJECT_NAME 变量 为名的可执行文件。      


```cmake 
target_link_libraries(${PROJECT_NAME} ${WORKFLOW_LIB})
```       
可以写在 `add_executable` 后面，为 PROJECT_NAME 可执行文件链接库文件，可以是动态的也可以是静态的。         
实在不能理解 target_link_libraries, link_directories, include_directories 之间的关系，三者，缺一不可，缺哪一个都无法通过编译。        
按要求这一句话应当要指定库文件路径，但是加入的 workflow 中却全都是文件夹路径；按道理这里只需要加入库文件路径，但是 workflow 中却包含了头文件路径。         
当做模板记下吧，毕竟项目的核心问题也不再 cmake，可能多写写代码，多编译几份文件就熟悉和理解了。       