---
layout:     post
title:      Series Article of UbuntuOS -- 30         
subtitle:   cp 复制大量文件报错 Argument list too long    
date:       2022-03-03      
author:     OUC_LiuX     
header-img: img/wallpic02.jpg     
catalog: true
tags:
    - Ubuntu OS
---
同磁盘内转移包含 67k 文件的数据集，直接 cp 命令复制，报错 `bash: /bin/cp: Argument list too long` 。显然，不能一次性复制这么多文件。可以使用脚本走一个 for 循环，通过依次复制的方法达成目标：        
```bash        
for i in /path/to/dir/* ; do cp "$i" /path/to/other/dir/; done    
```       
但是效率会慢很多。