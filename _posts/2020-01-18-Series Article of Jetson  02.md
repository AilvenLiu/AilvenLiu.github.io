---
layout:     post
title:      Series Article of Jetson -- 02
subtitle:   Jetson Nano 使用实录02 -- 编译有 cuda 支持的 OpenCV         
date:       2021-11-16
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Jetson Nano
    - Ubuntu OS
    - OpenCV
---     

> From CSDN - [Jetson带CUDA编译的opencv4.5安装教程与踩坑指南，cmake配置很重要！](https://blog.csdn.net/weixin_39298885/article/details/110851373)，实测有效。           


## jtop 前后对比          
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/jetson/jetson01.jpg"></div>                  
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/jetson/jetson02.jpg"></div>           

## 安装       
1. 下载版本对应的opencv+opencv_contrib源码包：       
   opencv: https://github.com/opencv/opencv/releases          
   opencv-contrib: https://github.com/opencv/opencv_contrib/tags       

2. 解压opencv与opencv-contrib，并将opencv-contrib文件夹放到opencv文件夹中。       

3. 安装依赖：         
   ```shell      
   sudo apt-get install build-essential \       
   libavcodec-dev \
   libavformat-dev \
   libavutil-dev \
   libeigen3-dev \
   libglew-dev \
   libgtk2.0-dev \
   libgtk-3-dev \
   libjpeg-dev \
   libpostproc-dev \
   libswscale-dev \
   libtbb-dev \
   libtiff5-dev \
   libv4l-dev \
   libxvidcore-dev \
   libx264-dev \
   qt5-default \
   zlib1g-dev \
   libavresample-dev \
   gstreamer1.0-plugins-bad \
   pkg-config
   ```     
4. 进入opencv文件夹，创建build文件夹，进入build文件夹，**进行cmake配置**。      
   **CMake 很重要，很重要，很重要！**       

### CMake 配置成功案例（重要，重要，重要）        

```shell    
cmake -D CMAKE_BUILD_TYPE=RELEASE\
 -D CMAKE_INSTALL_PREFIX=/usr/local\
 -D OPENCV_EXTRA_MODULES_PATH=../opencv_contrib/modules\
 -D CUDA_ARCH_BIN='5.3'\
 -D WITH_CUDA=1\
 -D BUILD_opencv_python3=OFF \       
 -D BUILD_opencv_python2=OFF \
 -D WITH_V4L=ON\
 -D WITH_QT=ON\
 -D WITH_OPENGL=ON\
 -D CUDA_FAST_MATH=1\
 -D WITH_CUBLAS=1\
 -D OPENCV_GENERATE_PKGCONFIG=1\
 -D WITH_GTK_2_X=ON\
 -D WITH_GSTREAMER=ON ..
```       

### Cmake 参数含义理解         
```shell     
-D CMAKE_BUILD_TYPE=RELEASE \  # 表示编译发布版本          
-D CMAKE_INSTALL_PREFIX     \  # lib 库位置，一般指定为 /usr/local
-D OPENCV_EXTRA_MODULES_PATH \ # 指定 contrib modules 路径        
-D CUDA_ARCH_BIN='5.3'      \  # 指定 GPU 算力，nano 是 5.3，具体的去 NVIDIA 官网查询         
-D WITH_CUDA=1              \  # 使用 CUDA        
-D WITH_V4L=ON              \  # Video for Linux 支持          
-D WITH_QT=ON               \  # qt 支持，不知道有什么用，编译上不吃亏       
-D WITH_OPENGL=ON           \  # OpenGL 支持，不知道有什么用，编译上不吃亏       
-D CUDA_FAST_MATH           \  # 看名字是 cuda 快速数学支持，不知道有什么用，编译上不吃亏       
-D WITH_CUBLAS=1            \  # CUDA 基础线性分析子程。     
-D OPENCV_GENERATE_PKGCONFIG \ # 用于生成 opencvc.pc，支持 pkg-config 功能        
-D WITH_GTK_2_X=ON          \  # 解决 GTK-Error的关键       
-D BUILD_opencv_python2=ON  \  # python2 支持；不需要，就不编译了。     
-D BUILD_opencv_python3=ON  \  # python3 支持；不需要，就不编译了。     
-D WITH_GSTREAMER=ON        \  # gstreamer 可以使用 gpu 对视频和视频流进行硬解码，不占用 cpu 资源。项目要用，编译上。         
-D WITH_FFMPEG=OFF          \  # ffmpeg for video I/O，似乎 jetson nano 不支持 ffmpeg，不编译          
-D BUILD_EXAMPLES           \  # 官方解释为生成 样例代码，且 default=OFF ，但我们没有加这一项也找到了 examples 例程。可尝试加上。      
..                             # CMakeList.txt 在上一级目录
```      

5. make && sudo make install 编译安装。在 jetson nano 上，这一过程通常会超过十个小时。          
   
至此，安装成功，可以使用命令`pkg-config --modversion opencv4`查看是否安装成功以及版本号。      
如果程序编译报 “fatal error: opencv2/opencv.hpp: 没有那个文件或目录” 错误