---
layout:     post
title:      Series Article of UbuntuOS -- 16       
subtitle:   apt-get 被删除的解决方案                     
date:       2021-09-11
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Ubuntu OS
---

缘起使用 apt-get 安装某包的时候遇到依赖问题，网上找解决方案，一堆狗日的都在说 aptitude 多好多好，可以自动解决依赖。且不说他到底能不能自动解决依赖，这堆狗日的都不说 aptitude 的缺点：和 apt-get 不共存。     
这就导致，安装了 aptitude 不仅没有解决依赖问题，还把 apt-get 搞没了。      
狗日的，误人子弟。       

## 解决方案     

进入 [UbuntuUpdates](https://www.ubuntuupdates.org/) 这个网站如下：    
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/ubuntuSeries/ubuntu003.png"></div>   
搜索下载 apt，libapt-pkg-dev，和 ubuntu-keyring 三个包，分别如下：      

1. apt     
   <div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/ubuntuSeries/ubuntu004.png"></div>      
   点进去第一个。
   <div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/ubuntuSeries/ubuntu005.png"></div>       
   找到适合自己系统版本的 Version 点进去，建议选择 main 的 security 或 base 版本。比如我的机器是 ubuntu1804，就点进去 Release:bionic + Repository:main + Level:security 的 Version 点进去。如果不确定或拿不准自己的系统版本对应的是哪个 Release，一个个点进去看一下吧，下一级界面里写了。点进去如下界面：      
   <div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/ubuntuSeries/ubuntu006.png"></div>       
   在 Download 一栏里，下载合适的格式，比如我的系统是 64 位，就下载 `64-bit deb package` 。     
2. libapt-pkg-dev 和 ubuntu-keyring 同理。       
3. 在需要修复的机器上，依次，一定要，**依次！** 使用 `sudo dpkg -i` 安装 ubuntu-keyring, libapt-pkg-dev, 和 apt 三个包。如：    
   ```bash     
   $ sudo dpkg -i ubuntu-keyring_2016.10.27_all.deb
   $ sudo dpkg -i libapt-pkg5.0_1.8.0_amd64.deb
   $ sudo dpkg -i apt_1.7.0_amd64.deb
   ```      
4. 结束。键入 `apt-get moo` 出现一头牛就是成功了。如果还不行，没办法了，自求多福吧。      
   <div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/ubuntuSeries/ubuntu007.png"></div>       
   

