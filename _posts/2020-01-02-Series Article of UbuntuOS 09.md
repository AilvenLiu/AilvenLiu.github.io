---
layout:     post
title:      Series Article of UbuntuOS -- 09
subtitle:   grep 文本搜索和 sed 文本替换               
date:       2021-07-21
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Ubuntu OS
---


> 参照 《Linux Shell 脚步攻略（第二版）》（人民邮电出版社）。     

## 使用 grep 在文件中搜索文本     

grep 最大的特性是接受正则表达。下面依照《Linux Shell 脚步攻略（第二版）》（人民邮电出版社）第四章三节的相关内容做出介绍。       

###  基本使用     
1. 搜索包含特定模式的文本行：      
   ```shell    
   $ grep pattern filename     
   this is a line containing pattern       

   $ grep "pattern" filename       
   this is a line containing pattern       
   ```     
   
