---
layout:     post
title:      Series Article of Jetson -- 04
subtitle:   Jetson Nano 使用实录04 -- GStreamer 解码本地视频或rstp 网络流         
date:       2021-11-22
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Jetson Nano
    - OpenCV
---     
需要保证 jetson nano 上有 gstreamer，需要保证 opencv 编译的时候开启了 gstreamer 和 cuda 。          

1. 解码本地视频。            
   ```c++
   cv::videoCapture cap("filesrc location=path/to/file.mp4 ! qtdemux ! h263parse ! nvv4l2decoder ! nvvidconv ! video/x-raw, format=BGRx ! videoconvert ! video/x-raw, format=BGR ! appsink", cv::CAP_GSTREAMER);
   ```           
   不要问含义，很玄学。这样能运行。再动任何地方，极有可能就不能运行了。        

2. 解码 rstp 网络流          
   ```c++       
   cv::videoCapture cap("rtspsrc location=rtsp://admin:123456@192.168.1.12/stream0 ! rtph264depay ! h264parse ! omxh264dec ! nvvidconv ! video/x-raw, width=1920, height=1056, format=BGRx ! videoconvert ! appsink", cv::CAP_GSTREAMER);
   ```         
   不要问为什么，这样能运行。试了几百次不同的参数组合，只有这样能运行。可能换个摄像头又不行了，和摄像头也有关系。       
