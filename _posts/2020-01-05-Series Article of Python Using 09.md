---
layout:     post
title: Python 与 Linux/C++ 进行 SOCKET 通信的注意事项
subtitle:   redis              
date:       2022-07-27
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - c++
    - python
---      

1. python 做客户端容易在 C++ 服务端产生客户端滞留现象，也即，虽然你发送完数据立刻 close 了socket 套接字，但是服务端依然疯狂的在读取缓存区，虽然什么都读不到。       
2. 为应对 1 所述现象，实测 close 之前先 sleep(1) 休眠一秒是可以的。      
3. 1 所属现象产生的原因也有可能是字符串未对齐所导致，这是由于 Linux/c++ 端会按照4个字节为一单位读取字符串，而 python 编码字符串的时候没有默认 4 字节对齐。此时需要我们 struct.pack(format, argv...) 进行手动对齐。    

4. 补充，3 所述可能性并不是导致 1 所述现象的原因，测试只建立连接不发送数据然后立刻关闭的情况依然会在服务端产生客户端滞留现象。