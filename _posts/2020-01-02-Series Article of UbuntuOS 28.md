---
layout:     post
title:      Series Article of UbuntuOS -- 28         
subtitle:   挂载硬盘路径软连接到 home    
date:       2021-11-22      
author:     OUC_LiuX     
header-img: img/wallpic02.jpg     
catalog: true
tags:
    - Ubuntu OS
---

服务器 /home 快满了，挂载了一块 4T 机械盘到 /data/disk2 作为数据盘（硬盘挂载相关问题见 [挂载外部存储设备相关问题](https://www.ouc-liux.cn/2021/11/11/Series-Article-of-UbuntuOS-25/)），可又不想每次读数据都用 /data/disk2/ailven/data/xxx 这样长的路径，于是建立一个软连接做个映射：       
```bash 
ln -s /data/disk2/ailven/data ~/data     
```
这样我的 /home 里就会出现一个 data 路径，而实际上这个路径虽然名字在 /home，其本体（所占用存储空间） 却在机械盘。       

通过 `rm -rf ~/data` 可以删除这个软连接