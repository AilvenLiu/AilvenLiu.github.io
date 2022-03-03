---
layout:     post
title:      Series Article of Git -- 03
subtitle:   git clone 出现 fatal unable to access https://github 类错误解决方法        
date:       2022-03-03
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Git
    - Github   
---     

可能是由于 git 对 http 协议 clone 的支持或网络连接不当等原因造成，将协议换为 git 大部分情况下可解决问题。如：        
```bash
git clone https://github.com/xxx/yyy.git
fatal: unable to access ‘https://github.com/xxx/yyy.git’: ......
```          
换为如下指令：         
```bash
git clone git://github.com/xxx/yyy.git         
```          
即可。     