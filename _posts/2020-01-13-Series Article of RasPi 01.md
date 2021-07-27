---
layout:     post
title:      Series Article of RasPi -- 01
subtitle:   树莓派使用实录01 -- 系统烧写和开机配置         
date:       2021-07-27
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - RasPi   
---     

## 系统烧写     

1. 下载烧写软件。
   在树莓派[官网](https://www.raspberrypi.org/software/)下载相应系统的烧写软件，我的工作主机是 ubuntu 系统，就以 [Imager for Ubuntu](https://downloads.raspberrypi.org/imager/imager_latest_amd64.deb) 进行介绍。    
   如果是树莓派系统，应当通过包管理器 `apt` 下载安装：    
   ```shell    
   $ sudo apt-get install rpi-imager
   ```     

2. 下载系统镜像。
   在树莓派[官网](https://www.raspberrypi.org/software/operating-systems/#raspberry-pi-os-32-bit)下载镜像，推荐 [Raspberry Pi OS with desktop](https://downloads.raspberrypi.org/raspios_armhf/images/raspios_armhf-2021-05-28/2021-05-07-raspios-buster-armhf.zip) 这个镜像，因为有图形界面，可以防不时之需。    
   然后 `sudo dpkg -i xxx` 安装即可。     

3. 烧写镜像到sd卡。     
   ```shell    
   $ rpi-imager
   ```    
   启动软件，当然将软件加到 dock 上最好了。界面如下：   
   <div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/raspi/raspi01.png"></div>    
   然后选择 sd 卡， 选择镜像。
   <div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/raspi/raspi02.png"></div>
   <div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/raspi/raspi03.png"></div>    

   镜像一定要从 `Use custom` 这一项中，选择下载好的镜像，如果选择 第一项 Raspberry Pi OS (32-bit) 这一项，会重新从网络下载，但下载源不在大陆，在墙外面，下载过程很费时间的。    
   点击 Write，输入 sudo 密码即可。大概等待十几分钟吧。    


## 无屏幕配置wifi 和 ssh 
自身     
