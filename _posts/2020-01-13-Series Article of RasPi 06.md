---
layout:     post
title:      Series Article of RasPi -- 06
subtitle:   树莓派使用实录06 -- 串口通信设置及例程          
date:       2021-10-11
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - RasPi   
---     

以树莓派4B为例子，3B/3B+过程略有不同，设置基本无异：

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

### 例程        
#### cpp     
```c++      
#include <errno.h>
#include <string.h>
#include <wiringPi.h>
#include <wiringSerial.h>
#include <cstdio>
#include <cstdlib>

char UartBuff[100];

int main(void)
{
	int fd;
	if ((fd = serialOpen("/dev/ttyAMA0", 115200)) < 0)
	{
		fprintf(stderr, "Unable to open serial device: %s\n", strerror(errno));
		return 1 ;
	}
	serialPuts(fd, "uart send test, just by launcher\n");
	
	while(1)
	{
		UartBuff[0] = serialGetchar(fd);
		if (UartBuff[0] > 0)
		{
			putchar(UartBuff[0]);	
			serialPutchar(fd,UartBuff[0]);
		}		
	}
	return 0;
}
```   
使用 USB 转 TTL 串口模块，USB 端接电脑，TTL 端接树莓派的 IO 口，实物连接图如下：        
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/raspi/raspi18.jpg"></div>        

运行在树莓派上编写完成的串口接收和发送程序，我们在 Windows 电脑端的串口调试助手接收到如下数据：              
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/raspi/raspi19.jpg"></div>        

接着，从串口助手发送“hello world!”给树莓派，树莓派接收到如下数据，并打印出来：  
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/raspi/raspi20.jpg"></div>        

串口助手也同时接收到如下信息：        
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/raspi/raspi21.jpg"></div>        

至此，证明树莓派串口设置成功。

#### python       
```python      
import serial
import time

ser = serial.Serial("/dev/ttyAMA0", 115200)


def main():
    while True:
        recv = get_recv()
        if recv != None:
            print recv
            ser.write(recv[0] + "\n")
        time.sleep(0.1)
    
            
def get_recv():
    cout = ser.inWaiting()
    if cout != 0:
        line = ser.read(cout)
        recv = str.split(line)
        ser.reset_input_buffer()
        return recv
 
   
if __name__ == "__main__":
    try:
        main()
    except KeyboardInterrupt:
        if ser != None:
            ser.close()
```       
