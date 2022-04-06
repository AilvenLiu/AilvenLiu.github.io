---
layout:     post
title:      Linux 网络编程学习笔记 -- 01          
subtitle:   创建套接字 socket 函数理解               
date:       2022-04-05
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Network Programming     
    - WebServer
--- 

<head>
    <script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
    <script type="text/x-mathjax-config">
        MathJax.Hub.Config({
            tex2jax: {
            skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
            inlineMath: [['$','$']]
            }
        });
    </script>
</head>   

### 函数        
socket 就是套接字，套接字就是 socket。`socket()` 函数用于创建一个新的socket，也就是向系统申请一个 socket 资源。       

一个创建监听 socket 文件描述符的实例如下：          
```c++
#include <sys/socket.h>             
#include <sys/types.h>           
int listenfd = socket(PF_INET, SOCK_STREAM, 0);
```      

其中， `socket()` 函数来自于 `sys/socket.h`，参数 PF_INET 和 SOCK_STREAM 来自于 `sys/socket.h`：         
```c++
#include <sys/socket.h>                  
int socket(int domain,int type,int protocol);
-> 成功时返回文件描述，失败时返回-1(linux)
```            

`socket()` 函数建立一个协议族为 domain、协议类型为 type、协议编号为protocol 的套接字文件描述符。如果函数调用成功，会返回一个标识这个套接字的文件描述符（整型数表示），失败的时候返回 -1。          

讨论其参数及含义：      

### 参数 domain          
参数 `domain` 用于设置网络通信的域，函数 `socket()` 根据这个参数选择通信协议的族。通信协议族在文件 `sys/socket.h` 中定义。常用的几种协议族包括：         
|名称|含义|
|:---|:---|
|PF_UNIX, PF_LOCAL|本地通信的UNIX协议族|
|AF_INET, PF_INET|IPv4 Internet 协议族|
|PF_INET6|IPv6 Internet 协议族|
|PF_IPX|IPX-Novell协议族|
|PF_PACKET|底层套接字的协议族|        

其中最常用的是 IPv4 Internet 协议族：`PF_INET`（ `AF_INET` ），这两者在定义中的值是相同的，也即具体使用上没有任何差别。           

### 参数 type            
参数 `type` 用于设置套接字通信的类型，主要有 `SOCKET_STREAM`（流式套接字）、`SOCK_DGRAM`（数据包套接字）等。`type` 允许的值及含义列表如下：         

|名称|含义|            
|:---|:---|            
|SOCK_STREAM|Tcp连接，提供序列化的、可靠的、双向连接的字节流。支持带外数据传输|         
|SOCK_DGREAM|支持UDP连接（无连接状态的消息）|        
|SOCK_SQLPACKET|序列化包，提供一个序列化的、可靠的、双向的基本连接的数据传输通道，数据长度定常。每次调用读系统调用时数据需要将全部数据读出|      
|SOCK_RAW|RAW类型，提供原始网络协议访问|         
|SOCK_RDM|提供可靠的数据报文，不过可能数据会有乱序|      
|SOCK_PACKET|一种专用类型，无法在应用程序中使用|      

**注意事项**          
* SOCK_STREAM 类型套接字表示一个双向的字节流，与管道类似。流式的套接字在进行数据收发之前必须已经连接，连接使用 `connect()` 函数进行。一旦连接，可以使用 `read()` 或者 `write()` 函数进行数据的传输。流式通信方式保证数据不会丢失或者重复接收，当数据在一段时间内任然没有接受完毕，可以将这个连接人为已经死掉。           
* SOCK_DGRAM 和 SOCK_RAW 两种套接字可以使用函数 `sendto()` 来发送数据，使用 `recvfrom()` 函数接受数据，`recvfrom()` 接受来自指定 IP 地址的发送方的数据。        
* SOCK_PACKET 是一种专用的数据包，它直接从设备驱动接受数据。       
* 并不是所有的协议族都实现了这些协议类型，例如，AF_INET 协议族就没有实现 SOCK_SEQPACKET 协议类型。         

### 参数 protocol          
参数 protocol 用于指定某个协议的特定类型，即 type 类型中的某个类型。有些协议有多种特定的类型，就需要设置这个参数来选择特定的类型。但是，通常，协议中只有一种特定类型，从而 protocol 参数仅能设置为 0。          

### 错误码 errno            
函数 `socket()` 并不总是执行成功，有可能会出现错误，错误的产生有多种原因，可以通过 `errno` 获得：       

|值|含义|       
|:--|:--|        
|EACCES|没有权限建立指定的 socket |
|EAFNOSUPPORT|不支持所指定的地址类型|
|EINVAL|不支持此协议或协议不可用|
|EMFILE|进程文件表溢出|   
|ENFILE|已经达到系统允许打开的文件数量上限，打开文件过多|
|ENOBUFS/ENOMEM|内存不足|
|EPROTONOSUPPORT|指定的协议在 domain 中不存在|

