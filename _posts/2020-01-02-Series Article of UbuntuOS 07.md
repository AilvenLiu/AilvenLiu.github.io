---
layout:     post
title:      Series Article of UbuntuOS -- 07 
subtitle:   vim 中的查找和替换          
date:       2021-07-09
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Ubuntu OS
    - vim
---

> 本意是写一篇 vim 专题博客或一个专题 series，把所有相关内容扔到里面。但好像没什么内容，遂没写；又但是，查找和替换还是挺重要的内容，单独在ubuntuOS series 下面开一篇博客吧。    

1. 英文字符下按下 `/` 自动进入控制栏，可进入查找模式。输入要查找的字符串并回车，光标会跳转从当前行向下的到第一个匹配。`n` 查找下一个，`N` （`shift + n`） 查找上一个。   

2. vim 支持正则表达式，比如控制栏里 `/cfg$` 即代表匹配位于行尾的 `cfg` 字符。有关正则表达式的更多内容可以看 UbuntuOS 系列第五篇博客： [linux中正则表达的一点儿小知识](https://www.ouc-liux.cn/2021/05/08/Series-Article-of-UbuntuOS-05/)