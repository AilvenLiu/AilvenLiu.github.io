---
layout:     post
title:      Series Article of UbuntuOS -- 23         
subtitle:   "Linux 下 fatal error: opencv2/opencv.hpp: 没有那个文件或目录"                   
date:       2021-11-02      
author:     OUC_LiuX     
header-img: img/wallpic02.jpg     
catalog: true
tags:
    - Ubuntu OS
    - OpenCV
---

主要问题是 /usr/local/include 文件夹中的结构是 include/opencv4/opencv2，
把 opencv2 创建一个软链接到父目录即可。

```
cd   /usr/local/include/
sudo  ln  -s   opencv4/opencv2   opencv2
```