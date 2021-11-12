---
layout:     post
title:      Series Article of UbuntuOS -- 26         
subtitle:   Ubuntu apt 下载报错先尝试换源更新   
date:       2021-11-11      
author:     OUC_LiuX     
header-img: img/wallpic02.jpg     
catalog: true
tags:
    - Ubuntu OS
---

1. 备份原有源。         
   ```bash   
   sudo cp /etc/apt/sources.list /etc/apt/sources.list_backup       
   ```

2. 换清华或阿里源（镜像）         
   编辑 /etc/apt/sources.list，替换原有内容为以下：     
   ```     
   // Ali:       
   deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse      
   deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiver     
   deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse         
   deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse       
   deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse         
   deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse      
   deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse       
   deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse      
   deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse      
   deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse       

   // or Tsinghua:       
   deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic main restricted universe multiverse      
   deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic main restricted universe multiverse      
   deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse     
   deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse     
   deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse    
   deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse     
   deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-security main restricted universe multiverse      
   deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-security main restricted universe multiverse       
   deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse         
   deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse        
   ```      

3. 更新        
   `sudo apt-get update`     
