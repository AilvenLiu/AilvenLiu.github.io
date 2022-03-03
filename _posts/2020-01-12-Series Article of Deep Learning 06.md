---
layout:     post
title:      Series Article of Deep Learning -- 06
subtitle:   Ubuntu1804卸载CUDA     
date:       2022-03-03
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Work Space        
    - Ubuntu OS
---
通过 CUDA 自带的卸载工具卸载，而不要粗鲁的删除 cuda 路径。      
```bash      
cd /usr/local/cuda/bin
sudo ./cuda-uninstaller
```       
完成卸载后检查一下 `~/.bashrc` 中的系统路径。       