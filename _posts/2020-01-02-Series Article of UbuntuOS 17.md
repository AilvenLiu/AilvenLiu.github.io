---
layout:     post
title:      Series Article of UbuntuOS -- 17         
subtitle:   使用/proc/pid挂载的方式隐藏进程           
date:       2021-09-12      
author:     OUC_LiuX     
header-img: img/wallpic02.jpg     
catalog: true
tags:
    - Ubuntu OS
---
用实验室服务器做点儿不可告人的事，但又不想被发现，于是考虑进程隐藏与伪装。考虑一个比较简单且有效的方法，就是 pid 挂载。原理如下：      
现在使用 `ps -ef | grep ssh` 查看本机所有包含关键词 ssh 的所有进程，如下：    
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/ubuntuSeries/ubuntu008.png"></div>       

比如我们现在想要隐藏一个 ssh 到创新中心 ailven 服务器的常驻进程，其 pid 为 10823 。事实上每个进程都会在 `\proc` 路径下建立一个名为 pid 的子路径，比如：    
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/ubuntuSeries/ubuntu009.png"></div>       

我们列出 `/proc` 下的所有文件和路径，发现一个名为 `10823` 的子路径。正因为存在这个子路径及子路径内的进程信息，我们使用 ps，top，或 nvidia-smi 等命令才可以找到相应进程。所以要隐藏指定的 pid 为 10823 的进程，只需要使系统找不到这个子路径。但又没办法删掉它。于是，非常显然的一个方法，只需要做一个伪装，使这个子路径指向本不属于它的路径即可。     
于是，找一个硬盘挂载到这个路径，这个路径吧，虽然还存在，进程本身也没有消失，但系统尝试进入这个路径的时候，却找不到进程信息。如果进程太多，真的每一个 pid 都要找一个硬盘挂载上，也不现实。      
使用 `--bind` 参数挂在一个虚拟的磁盘（一个普通的文件夹）即可。     
比如我们现在在 `～/` 家路径下有一个 tmp 路径：      
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/ubuntuSeries/ubuntu010.png"></div>       

将该子路径作为一个硬盘路径挂载到 `/proc/10823` 进程路径：     
```bash    
$ sudo mount --bind ~/tmp /proc/10823     
```        

现在再次 `ps` 命令查找关键词为 `ssh` 的进程，已经不见了踪影；使用 `ls` 查看 `/proc/10823` ，空空如也，不见了进程信息：      
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/ubuntuSeries/ubuntu011.png"></div>       

但回到 ssh 的界面，并没有退出。     
进程隐藏成功，此时使用 ps 和 top 等命令都检测不到正在运行的 pid 为 10823 的进程。如果你正在执行 GPU 进程，使用 nvidia-smi 同样也不会列出任何进程信息。唯一不足，在没有任何进程信息的情况下 GPU 使用率达到 99%，同样会令人怀疑。      
下一步如有机会考虑使用进程伪装代替进程隐藏，将不可告人的秘密进程的名字改成 `python train.py [argvs...]` 。     
