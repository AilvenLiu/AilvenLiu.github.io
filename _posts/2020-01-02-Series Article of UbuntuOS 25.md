---
layout:     post
title:      Series Article of UbuntuOS -- 25         
subtitle:   Ubuntu 无法挂载或无法写入外部存储设备问题                   
date:       2021-11-11      
author:     OUC_LiuX     
header-img: img/wallpic02.jpg     
catalog: true
tags:
    - Ubuntu OS
---

1. 无法挂在外部存储设备，提示 mount: wrong fs type, bad option, bad superblock on /dev/xxx, missing codepage or helper program, or other error：      
   原因：存储设备没有格式化或文件系统格式错误。通常 Ubuntu 系统可接受的文件格式为 ntfs 和 ext4。可以通过 linux 命令格式化为 ext4：      
   ```bash        
   sudo mkfs -t ext4 /dev/sdb           
   ```          
   或者在 win 设备上将之重新格式化为 ntfs/4k 格式。         

2. 无法向 ntfs 文件格式磁盘/存储设备写入数据，只能读取。           
   ```bash      
   sudo apt-get install ntfs-3g
   sudo ntfsfix /dev/path      
   ```     
   其中 path 是待修复的分区的位置，如果是桌面系统，可以在文件管理器查看。比如我要修复 Data 分区，查看文件系统则发现：       
   <div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/ubuntuSeries/ubuntu013.png"></div>        

   其位置为 /dev/sda1，于是将 path 替换为 sda1 。执行完命令后重新挂载即可正常写入。      

   如果是服务端系统，可通过 fdisk -l 命令查看。但 fdisk 不能显示 win 系统下设定的分区（磁盘），只能通过分区大小进行判断。       
   