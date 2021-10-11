---
layout:     post
title:      Series Article of RasPi -- 03
subtitle:   树莓派使用实录04 -- 快速安装 torch & torchvision           
date:       2021-08-01
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - RasPi   
    - pytorch        
---     

1. 在 [Google Cloud Drive](https://drive.google.com/drive/folders/1jCBjlcZd6RN7wsI0nbk2M0HtAJaEPQ_-?usp=sharing) 下载 pytorch4raspi 文件夹，文件夹里包含适用于arm-v7 的 torch-1.3.0 和 torchvision-0.4 两个 whl 文件。    
2. raspi 内指定 conda 环境下执行以下两行命令本地安装：      
   ```bash      
   $ pip install torch-1.3.0a0+de394b6-cp37-cp37m-linux_armv7l.whl      
   $ pip install torchvision-0.4.1a0+a263704-cp37-cp37m-linux_armv7l.whl 
   ```      
   根据自己情况使用 pip 或 pip3。我的板子是 python 裸奔，默认 python 为 3.7，于是使用了 pip3 install .    
4. 结束，特别快速。     
   
