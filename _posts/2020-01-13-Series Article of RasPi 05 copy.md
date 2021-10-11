---
layout:     post
title:      Series Article of RasPi -- 06
subtitle:   树莓派使用实录04 -- 串口通信          
date:       2021-10-11
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - RasPi   
---     

以树莓派4B为例子，3B/3B+过程略有不同，设置无异：

### 外设IO口定义说明

从树莓派的相关资料我们可以看到，树莓派有两个串口可以使用，一个是硬件串口（/dev/ttyAMA0）,另一个是mini串口（/dev/ttyS0）。硬件串口有单独的波特率时钟源，性能好，稳定性强；mini串口功能简单，稳定性较差，波特率由CPU内核时钟提供，受内核时钟影响。           
树莓派（3/4代）板载蓝牙模块，默认的硬件串口是分配给蓝牙模块使用的，而性能较差的mini串口是分配给GPIO串口 TXD0、RXD0。       
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/raspi/raspi07.png"></div>      

### Serial 配置          
首先运行ls /dev -al命令查看到默认的串口分配方式，如下图所示：      
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/raspi/raspi08.png"></div>      

由于硬件串口分配给板载蓝牙使用，所以要释放掉,并设置硬件串口分配给GPIO串口。
首先登陆终端后，输入sudo raspi-config命令进入树莓派系统配置界面,选择第五个Interfacing Options：             
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/raspi/raspi09.png"></div>      

进入P6 Serial          
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/raspi/raspi10.png"></div>      

选择关闭串口登录功能，打开硬件串口调试功能：         
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/raspi/raspi11.png"></div>      
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/raspi/raspi12.png"></div>      

完成后提示以下界面，按 OK           
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/raspi/raspi13.png"></div>      

### 设置硬件串口为GPIO串口          
接着将串口配置为我们的GPIO串口，`sudo vim /boot/config.txt`，将下面两行内容添加到最后: 
```shell 
dtoverlay=pi3-miniuart-bt 
force_turbo=1        
enable_uart=1 #raspi3 需要这一项，4 应该不用 
```         
修改后保存并退出，内容如下图片所示：            
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/raspi/raspi14.png"></div>      

**请注意：树莓派4b也是写pi3**。交换映射完成。重启树莓派后，再次输入 `ls /dev-al`，可以看到两个串口已经互相换了位置：         
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/raspi/raspi15.png"></div>      

### minicom串口助手测试              
```shell       
$ sudo apt-get install minicom  # 安装 minicom          

$ minicom -D /dev/ttyAMA0 -b 9600  
```       
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/raspi/raspi16.png"></div>        

其中-D表示选择串口/dev/ttyAMA0，-b 设置波特率为9600，此参数可以不用设置，默认11520，并且退出minicom时需要先按Ctrl+A，再按Z，弹出以下菜单，0。       
将树莓派与TTL对应接上，就可以通过PC串口助手与树莓派互相发送、接收数据，但是树莓派在发送数据时，命令终端并不会显示，如下图所示：          
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/raspi/raspi17.png"></div>        