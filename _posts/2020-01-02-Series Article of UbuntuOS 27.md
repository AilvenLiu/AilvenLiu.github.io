---
layout:     post
title:      Series Article of UbuntuOS -- 27         
subtitle:   cuDNNv8 强烈建议 deb 方法安装   
date:       2021-11-22      
author:     OUC_LiuX     
header-img: img/wallpic02.jpg     
catalog: true
tags:
    - Ubuntu OS
    - Work Space
---

Nvidia cuDNN 加速库自 v8.x 开始不再支持 tar 包通过验证 /usr/local/cuda/include/cudnn.h 文件中是否存在 CUDNN_MAJOR 信息的方式确认是否成功安装。这是因为在cudnn8里面cudnn.h已经没有CUDNN_MAJOR这个信息。我们强烈建议使用更方便快捷简单有效的 deb 方法安装。          

1. 下载安装          
   在 [官网下载平台](https://developer.nvidia.com/rdp/cudnn-archive) 找到需要的版本，下载适合自己 ubuntu 版本的三个 deb 包。          
   <div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/ubuntuSeries/ubuntu014.png"></div>       

   比如我 ubuntu1804 的机器需要 8.2.0 的 cnDNN，就下载如下三项：         
   ```
   cuDNN Runtime Library for Ubuntu18.04 (Deb)        
   cuDNN Developer Library for Ubuntu18.04 (Deb)         
   cuDNN Code Samples and User Guide for Ubuntu18.04 (Deb)      
   ```        

   依次，一定要 **依次** 安装：      
   ```bash         
   sudo dpkg -i libcudnn8_xxx+cudaxxx_amd64.deb        
   sudo dpkg -i libcudnn8-dev_xxx+cudaxxx_amd64.deb       
   sudo dpkg -i libcudnn8-samples_xxx+cudaxxx_amd64.deb
   ```     
2. 测试。           
   To verify that cuDNN is installed and is running properly, compile the mnistCUDNN sample located in the /usr/src/cudnn_samples_v8 directory in the debian file.        
   ```bash          
   cp -r /usr/src/cudnn_samples_v8/ $HOME   # 一定要 cp 整个文件夹
   cd ~/cudnn_samples_v8/mnistCUDNN   # 一定要进入到 mnistCUDNN 子路径      
   sudo make clean        
   sudo make         
   sudo ./mnistCUDNN           
   ```
   If cuDNN is properly installed and running on your Linux system, you will see a message similar to the following:         
   ```        
   Test Passed!       
   ```      
3. 编译mnistCUDNN时出错：fatal error: FreeImage.h: No such file or directory：        
   apt-get 安装缺失文件            
   ```bash          
   sudo apt-get install libfreeimage3 libfreeimage-dev
   ```     
   重新编译即可。          

           
4. 卸载。           
   ```bash           
   sudo rm -rf /usr/local/cuda/include/cudnn.h         
   sudo rm -rf /usr/local/cuda/lib64/libcudnn*
   sudo rm -rf /usr/include/cudnn.h        
   sudo rm -rf /usr/lib/x86_64-linux-gnu/libcudnn*          
   ```