---
layout:     post
title:      Win:This Python interpreter is in a conda environment, but the environment has not been activated.
subtitle: Win下的conda 工作环境bug
date:       2020-01-02
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - work space
---
When you are using anaconda on a windows PC, the above error may occurs when you attempt to create a virtual python environment and activate it. 
You may failed to activate it, i.e. if you obvious your python version via `python -V`, it show you the original version of anaconda base. 
Then run `conda init` and close and restart a shell, the mistake may be solved.