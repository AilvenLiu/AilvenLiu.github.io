---
layout:     post
title:      Series Article of Jetson -- 04
subtitle:   Jetson Nano 使用实录03 -- GStreamer 解码本地视频或rstp 网络流         
date:       2021-11-21
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Jetson Nano
    - OpenCV
---     
售货柜项目需要设计一个开关电路：当柜子门关上，按键开关连通，目标检测程序关闭；柜门打开，按键开关断开，目标检测程序启动。使用 GPIO 接收外置电平信号即可。          

## Jetson nano GPIO 40 引脚实拍图和位置图          
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/jetson/jetson03.jpg"></div>       

<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/jetson/jetson04.png"></div>       

## c++ 例程及注意事项        

一个 c++ 例程见 [jetsonNanoGPIOExample](https://github.com/OUCliuxiang/jetsonNanoGPIOExample/tree/main)。该例程使用 GPIO_PE6 （BOARD 模式 33 号引脚） 做输入接收电平信号，用 I2S4_SDIN （BOARD 模式 38 号引脚） 做输出驱动一个 led 灯。 使用一个 220Ω 电阻避免短接，将 vcc5 接出的电压稳定在 5v 。           

需要注意的是，在执行程序之前，需要通过 python 将 33 号引脚的模式设置为 input：       
```python    
import RPi.GPIO as GPIO        
GPIO.setmode(GPIO.BOARD)
GPIO.setup(33, GPIO.IN)
```