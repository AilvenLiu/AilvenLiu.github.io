---
layout:     post
title:      Series Article of UbuntuOS -- 24         
subtitle:   Ubuntu 网络问题万金油                   
date:       2021-11-11      
author:     OUC_LiuX     
header-img: img/wallpic02.jpg     
catalog: true
tags:
    - Ubuntu OS
---

> 记录一些连接有线以太网的服务器端遇到的网络问题及万金油解决方案。          

### 1. ifconfig 找不到有线网卡       
这种情况下 ifconfig 命令的返回信息只有一个 lo 本地回环信息，说明有线网卡被关掉了。使用 `ip addr` 命令查找本机网卡信息，加入找到的网卡是 etn1 ，将之挂载：     
```bash      
sudo ifconfig etn1 up    // 或者    
sudo ifup etn1     
```     

### 2. ifconfig 能找到有线网卡，但没有静态ip, address, netmask, gateway 等信息           

需要重新配置网卡。依然假如我们要配置的有线网卡（得是插着插着网线的那一个，如果有多个网卡但不知道哪个接网线，一个个试吧）是 etn1，编辑 `/eth/network/interfaces` 文件，加入以下信息：         
```       
auto etn1 // 开机自启动
iface etn1 inet static // 手动设置，可选项有 dhcp 接受路由分配地址
address 192.168.123.124 //（ip地址）
netmask 255.255.255.0 //（子网掩码）
gateway 192.168.123.1 //（网关）
```     
其中网关和子网掩码可以在一个连接到当前网络的机器上执行 ifconfig/ipconfig 获取。       
然后 `sudo dhclient etn1` 更新 ip 地址，或许还要重启一次机器。       

### 3. 只能 Ping 局域网内主机，ping 互联网上的 ip 提示 Ubuntu network is unreachable       
可能需要重新设置一下 dns 。编辑 /etc/resolvconf/resolv.conf.d/base，加入以下内容:       
```
nameserver 8.8.8.8          
nameserver 8.8.4.4
``` 
执行 recolvconf -u 命令更新 dns。     

### 4. 其他疑难杂症：重启网络           
1. 关闭网络 sudo service network-manager stop 或 sudo /etc/init.d/networking stop        
2. 删除状态 sudo rm /var/lib/NetworkManager/NetworkManager.state         
3. 启动网络 sudo service network-manager start 或 sudo /etc/init.d/networking stare        
