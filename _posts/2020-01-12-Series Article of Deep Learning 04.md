---
layout:     post
title:      Series Article of Deep Learning -- 04
subtitle:   Pytorch训练意外停止显存不释放     
date:       2022-03-03
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - pytorch        
    - Ubuntu OS
---

使用 `ctrl+c` 强行中断 pytorch 训练过程，往往会发生GPU任务终止（利用率为零）但显存未释放的问题。该状态下查看 `nvidia-smi` GPU 状态会发现没有相关任务运行，但使用 `ps -ef | grep xxx` 指令查找则会发现相关进程仍在运行。需要手动杀死。      
但是当任务并行运行时，比如多个 worker 并行加载数据的进程，一次可能面临数十个需要手动停止的进程，此时一个个手动输入 pid 效率较低，建议采用根据关键字自动 Kill 的方法，指令如下：          
```bash         
ps -ef | grep key1 | grep key2 | awk '{print $2}' | xargs kill -9 
```        
解释可见[crontab 定时启动和杀死任务](https://www.ouc-liux.cn/2021/09/13/Series-Article-of-UbuntuOS-18/) 。          