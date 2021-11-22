---
layout:     post
title:      Series Article of Jetson -- 02
subtitle:   Jetson Nano 使用实录02 -- 编译有 cuda 支持的 OpenCV4.5         
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
   我选的是 opencv4.5      

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
**重要，注意：不要换行，不要换行，不要换行！！**
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
-D WITH_CUDA=1              \  # 使用 CUDA，非必选项，可以不用。        
-D WITH_V4L=ON              \  # Video for Linux 支持          
-D WITH_QT=ON               \  # qt 支持，不知道有什么用，编译上不吃亏       
-D WITH_OPENGL=ON           \  # OpenGL 支持，不知道有什么用，编译上不吃亏       
-D CUDA_FAST_MATH           \  # 看名字是 cuda 快速数学支持，不知道有什么用，编译上不吃亏       
-D WITH_CUBLAS=1            \  # CUDA 基础线性分析子程。     
-D OPENCV_GENERATE_PKGCONFIG \ # 用于生成 opencvc.pc，支持 pkg-config 功能        
-D WITH_GTK_2_X=ON          \  # 解决 GTK-Error的关键       
-D BUILD_opencv_python2=OFF \  # python2 支持；不需要，就不编译了；而且编译容易出错。     
-D BUILD_opencv_python3=OFF \  # python3 支持；不需要，就不编译了；而且编译容易出错。     
-D WITH_GSTREAMER=ON        \  # gstreamer 可以使用 gpu 对视频和视频流进行硬解码，不占用 cpu 资源。项目要用，编译上。         
-D WITH_FFMPEG=OFF          \  # ffmpeg for video I/O，似乎 jetson nano 不支持 ffmpeg，不编译          
-D BUILD_EXAMPLES           \  # 官方解释为生成 样例代码，且 default=OFF ，但我们没有加这一项也找到了 examples 例程。可尝试加上。      
..                             # CMakeList.txt 在上一级目录
```    

5. make -j && sudo make install 编译安装。在 jetson nano 上，这一过程通常会超过十个小时。         
6. 关于上一步，刘文告诉我 `make -j` 自动调用 cpu 所有核，容易把自己卡死，所以慢。如果少用一个核，比如 Jetson nano 有 4 个核， 我用 `make -j3` 就会快很多。还没有试，有时间试一试回来补上。         
   
至此，安装成功，可以使用命令`pkg-config --modversion opencv4`查看是否安装成功以及版本号。      

## 报错与解决指南           
百分之九十九会出现的错误，建议在 cmake 之前先无脑配置，省得遇到错误再来看，耽误几个小时。        

### fatal error: boostdesc_bgm.i: No such file or directory        
境内编译 opencv 十有八九会遇到的一个错误：         
```
fatal error: boostdesc_bgm.i: No such file or directory
Makefile:162: recipe for target ‘all’ failed
make: *** [all] Error 2
```
由于长城的存在，部分文件下载超时，导致了这个问题。     

#### 解决方案 1：       
进入到 opencv 根路径，保存执行以下脚本：         
```bash
#!/bin/bash
cd ./cache/xfeatures2d/
cd boostdesc

curl https://raw.githubusercontent.com/opencv/opencv_3rdparty/34e4206aef44d50e6bbcd0ab06354b52e7466d26/boostdesc_lbgm.i > 0ae0675534aa318d9668f2a179c2a052-boostdesc_lbgm.i
curl https://raw.githubusercontent.com/opencv/opencv_3rdparty/34e4206aef44d50e6bbcd0ab06354b52e7466d26/boostdesc_binboost_256.i > e6dcfa9f647779eb1ce446a8d759b6ea-boostdesc_binboost_256.i
curl https://raw.githubusercontent.com/opencv/opencv_3rdparty/34e4206aef44d50e6bbcd0ab06354b52e7466d26/boostdesc_binboost_128.i > 98ea99d399965c03d555cef3ea502a0b-boostdesc_binboost_128.i
curl https://raw.githubusercontent.com/opencv/opencv_3rdparty/34e4206aef44d50e6bbcd0ab06354b52e7466d26/boostdesc_binboost_064.i > 202e1b3e9fec871b04da31f7f016679f-boostdesc_binboost_064.i
curl https://raw.githubusercontent.com/opencv/opencv_3rdparty/34e4206aef44d50e6bbcd0ab06354b52e7466d26/boostdesc_bgm_hd.i > 324426a24fa56ad9c5b8e3e0b3e5303e-boostdesc_bgm_hd.i
curl https://raw.githubusercontent.com/opencv/opencv_3rdparty/34e4206aef44d50e6bbcd0ab06354b52e7466d26/boostdesc_bgm_bi.i > 232c966b13651bd0e46a1497b0852191-boostdesc_bgm_bi.i
curl https://raw.githubusercontent.com/opencv/opencv_3rdparty/34e4206aef44d50e6bbcd0ab06354b52e7466d26/boostdesc_bgm.i > 0ea90e7a8f3f7876d450e4149c97c74f-boostdesc_bgm.i
cd ../vgg
curl https://raw.githubusercontent.com/opencv/opencv_3rdparty/fccf7cd6a4b12079f73bbfb21745f9babcd4eb1d/vgg_generated_120.i > 151805e03568c9f490a5e3a872777b75-vgg_generated_120.i
curl https://raw.githubusercontent.com/opencv/opencv_3rdparty/fccf7cd6a4b12079f73bbfb21745f9babcd4eb1d/vgg_generated_64.i > 7126a5d9a8884ebca5aea5d63d677225-vgg_generated_64.i
curl https://raw.githubusercontent.com/opencv/opencv_3rdparty/fccf7cd6a4b12079f73bbfb21745f9babcd4eb1d/vgg_generated_48.i > e8d0dcd54d1bcfdc29203d011a797179-vgg_generated_48.i
curl https://raw.githubusercontent.com/opencv/opencv_3rdparty/fccf7cd6a4b12079f73bbfb21745f9babcd4eb1d/vgg_generated_80.i > 7cd47228edec52b6d82f46511af325c5-vgg_generated_80.i
```
重新编译。     
#### 解决方案 2        
下载 [缓存包](https://github.com/OUCliuxiang/xfeatures2d_packages_for_opencv4.5/raw/main/patch__.zip)，解压后得到两个文件夹 boostdesc 和 vgg，放到 `opencv/.cache/xfeatures2d/` 路径下面。重新编译。      

#### 解决方案 3         
这个，不一定好用。      
下载 [源码文件](https://github.com/OUCliuxiang/xfeatures2d_packages_for_opencv4.5/raw/main/boostdesc_bgm.i%2Cvgg_generated_48.i%E7%AD%89.rar) ，解压后是一堆 .i 文件，放到 opencv_contrib/modules/xfeatures2d/src/ 路径下，重新编译。         

### fatal error: features2d/test/test_detectors_regression.impl.hpp: No such file or directory       

头文件 include 地址不对，将 opencv/modules/features2d/test 路径下的       
```bash
test_descriptors_invariance.impl.hpp
test_descriptors_regression.impl.hpp
test_detectors_invariance.impl.hpp
test_detectors_regression.impl.hpp
test_invariance_utils.hpp
``` 
拷贝到opencv_contrib/modules/xfeatures2d/test/文件下。         

同时，将opencv_contrib/modules/xfeatures2d/test/test_features2d.cpp文件下的
```c++
#include "features2d/test/test_detectors_regression.impl.hpp"
#include "features2d/test/test_descriptors_regression.impl.hpp"
```      
改成     
```c++    
#include "test_detectors_regression.impl.hpp"
#include "test_descriptors_regression.impl.hpp"
```        
将 opencv_contrib/modules/xfeatures2d/test/test_rotation_and_scale_invariance.cpp 文件下的        
```c++
#include "features2d/test/test_detectors_invariance.impl.hpp" 
#include "features2d/test/test_descriptors_invariance.impl.hpp"      
```        
改成：        
```c++
#include "test_detectors_invariance.impl.hpp"
#include "test_descriptors_invariance.impl.hpp"
```        

#### 编译成功，实际使用时 fatal error: opencv2/opencv.hpp: No such file or directory          

主要问题是 /usr/local/include 文件夹中的结构是 include/opencv4/opencv2， 把 opencv2 创建一个软链接到父目录即可。见 [Linux 下 fatal error: opencv2/opencv.hpp: 没有那个文件或目录](https://www.ouc-liux.cn/2021/11/02/Series-Article-of-UbuntuOS-23/)       
```shell      
cd /usr/local/include/
sudo ln -s opencv4/opencv2 opencv2
```