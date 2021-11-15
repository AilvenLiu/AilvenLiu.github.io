---
layout:     post
title:      Series Article of Jetson -- 01
subtitle:   Jetson Nano 使用实录01 -- Linux 编译 protobuf         
date:       2021-11-02
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Jetson Nano
    - Ubuntu OS
---     

1. 安装依赖                 
   ```
   sudo apt-get install autoconf automake libtool curl make g++ unzip
   ```
2. 下载源码
   项目requirements.txt指定了版本号为 3.11.4，在 https://github.com/protocolbuffers/protobuf/tags 通过 tag 寻找。使用 wget 或点击下载：
   https://github.com/protocolbuffers/protobuf/releases/download/v3.11.4/protobuf-cpp-3.11.4.tar.gz             
3. 解压
   ```
   tar -zxvf protobuf-cpp-3.11.4.tar.gz
   ```
4. 依照 github readme.md 文档依次运行以下命令：
   ```
   cd protobuf-3.7.1
   ./autogen.sh
   ./configure
   make
   make check
   sudo make install
   sudo ldconfig
   ```
5. 如无意外，编译成功。此时通过 `protoc --version` 查看版本，终端打印出信息：`libprotoc 3.11.4`。           
