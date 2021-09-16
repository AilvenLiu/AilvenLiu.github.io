---
layout:     post
title:      Series Article of UbuntuOS -- 22         
subtitle:   压缩/解压缩 tar.xz 文件                   
date:       2021-09-16      
author:     OUC_LiuX     
header-img: img/wallpic02.jpg     
catalog: true
tags:
    - Ubuntu OS
    - 
---

.xz ，虽然很少被使用，以至于极其罕见，于是其解压命令也少为人知。但这是绝大数 Linux 默认就带的一个压缩工具。压缩率相当高，比 7z 不相上下，甚至更高。唯一一点不足是压缩时间稍长，但无妨。      

## 压缩为 .xz 文件
```shell
$ xz -z source.tar
```      

如果要保留被压缩的文件加上参数 `-k` ，如果要设置压缩率加入参数 `-0` 到 `-9` 调节压缩率。如果不设置，默认压缩等级是6。      

## 解压缩 .xz 文件      
```shell     
$ xz -d target.tar.xz       
```      

同样使用 `-k` 参数来保留被解压缩的文件。

## 创建或解压 .tar.xz 文件 

习惯了 `tar czvf` 或 `tar xzvf` 的人可能碰到 `tar.xz` 也会想用单一命令搞定解压或压缩。其实不行 `tar` 里面没有征对xz格式的参数，比如 `z` 是针对 `gzip`，`j` 是针对 `bzip2`。    

**创建 .tar.xz 文件：**     
先 `tar cvf xxx.tar xxx/` 创建 `xxx.tar` 文件，然后使用 `xz -z xxx.tar` 来将 `xxx.tar` 压缩成为 `xxx.tar.xz`。       


**解压tar.xz文件：**：        
先 `xz -d xxx.tar.xz` 将 `xxx.tar.xz` 解压成 `xxx.tar` 然后，再用 `tar xvf xxx.tar` 来解包。   

