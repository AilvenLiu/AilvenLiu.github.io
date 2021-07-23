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

2. 从标准输入流 stdin 中读入数据（管道操作符）：     
   ```shell    
   $ echo "this is a word\nsecond line" | grep word        
   this is a word       
   ```     

3. 对多个文件进行搜索，比如当前路径下 grep_test1.txt, grep_test2.txt 分别有"patterned1"，"patterned2"这两行内容：       
   ```shell      
   $ grep "pattern" grep_te*      
   grep_test1.txt:patternned1
   grep_test2.txt:patternned2
   ```     
   好像不打印到底在第几行。     

4. 使用 `--color` 参数在屏幕输出中着重标记匹配到的字符串：     
    ```shell      
    $ grep "pattern" grep_te*      
    grep_test1.txt:patternned1
    grep_test2.txt:patternned2
   ```      
   Markdown 上体现不出来，在我的 linux terminal 里面，pattern这个字符串是红色的。    
   而且，至少 bash 环境下即使不加 `--color` 也会着重显示，似乎是缺省值就是 `auto`。但可以选择 `--color=never` 关闭颜色。      
   `--color` 参数有 always, never, auto 三个选项。    

5. 使用 `-E` 参数开启[正则表达](https://www.ouc-liux.cn/2021/05/08/Series-Article-of-UbuntuOS-05/)匹配：
    ```shell
    $ grep -E "[a-z]+" filename        
    ```    
    匹配 filename 中的任意单词。尝试了几个案例，正则表达式不在双引号之内似乎不行。    

6. 使用 `-o` 参数只打印文件匹配到的文本部分：     
    ```shell     
    $ echo this is a line. | grep -o -E "[a-z]+\."
    line.    
    ```

7. 使用 `-v` 参数打印匹配行之外的所有行：   
    ```shell   
    $ echo -e "this is a word.\nnext line." | grep -v "word"     
    next line.    
    ```    

8. 使用 `-c` 参数统计包含匹配字符串的行数（不是次数）：    
    ```shell     
    $ echo -e "pattern1, pattern2\npatern03\nptern" | grep -c "pattern"
    1
    ```    
    显然由于只有第一行包含了两个"pattern"，于是输出为 1 。