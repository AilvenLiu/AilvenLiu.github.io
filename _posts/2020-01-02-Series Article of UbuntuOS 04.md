---
layout:     post
title:      Series Article of UbuntuOS -- 04 
subtitle:   一些常用的linux命令     
date:       2020-05-07
author:     OUC_LiuX
header-img: img/wallpic_02.jpg
catalog: true
tags:
    - Ubuntu OS
---

> 记录一些自己常用的Linux命令。    

## 实时查看N卡状态     
```bash    
watch -n 1 nvidia-smi    
```   
其中 `-n`指定刷新时间，单位为秒。上面指令的意思就是以每秒刷新一次的频率显示`nvidia-smi` 内容。      

## 查看cpu及内存实时状态    
```bash    
htop   
```    
和`top`命令类似，但更加高大上，如下图。      
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/ubuntuSeries/ubuntu-001.png"></div>    