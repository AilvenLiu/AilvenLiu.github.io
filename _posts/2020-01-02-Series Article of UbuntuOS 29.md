---
layout:     post
title:      Series Article of UbuntuOS -- 29         
subtitle:   Linux多线程压缩软件pigz    
date:       2022-03-03      
author:     OUC_LiuX     
header-img: img/wallpic02.jpg     
catalog: true
tags:
    - Ubuntu OS
---

服务器 Ubuntu 自带的 zip 和 tar 都是单线程压缩工具，压缩大型文件项目有些力不从心，一核有难，众核围观。pigz 是一款多线程压缩的工具，是GZip的并行版(pigz，即 Parallel Implementation of GZip)，作者为Mark Adler。    
apt 可安装：        
```bash     
sudo apt-get install pgiz          
```          
常用参数有：             
* -0 ~ -9 压缩等级，数字越大压缩率越高，速度越慢，默认为6           
* k --keep 压缩后不删除原始文件          
* -l --list 列出压缩输入的内容            
* -K --zip Compress to PKWare zip (.zip) single entry format           
* -d --decompress 解压缩输入         
* -p --processes n 使用n核处理，默认为使用所有CPU核心         


pigz 单独使用只能压缩单个文件，如果要压缩多个文件（文件夹），需要配合打包工具 tar 使用：         
```bash         
tar -cvf dir1 dir2 dir3 ... | pigz [args] > output.tar.gz           
```           
