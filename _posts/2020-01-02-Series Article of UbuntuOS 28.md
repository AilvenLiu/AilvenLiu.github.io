---
layout:     post
title:      Series Article of UbuntuOS -- 28         
subtitle:   Ubuntu 挂载新硬盘非 root 无法写入的问题   
date:       2021-11-22      
author:     OUC_LiuX     
header-img: img/wallpic02.jpg     
catalog: true
tags:
    - Ubuntu OS
---

挂载点路径没有开放写入权限，赋予完全开放权限即可。比如我挂载了一个硬盘到 /data/disk2 路径，则：        
```
sudo chmod 777 /data/disk2 
```
就可了。 