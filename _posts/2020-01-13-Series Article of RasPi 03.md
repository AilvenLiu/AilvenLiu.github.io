---
layout:     post
title:      Series Article of RasPi -- 03
subtitle:   树莓派使用实录03 -- NCS2部署yolov5模型              
date:       2021-07-30
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - RasPi   
    - pytorch    
    - object detection    
---     

首先，备好两个pytorch(gpu) 版本，一个应当是 1.7.1 以上，用来执行 yolov5 网络训练与推理测试。一个 1.5 以下版本，这是由于高版本 torch 似乎会在安装（使用） onnx 的过程中出现问题。
