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

8. 使用 `-c` 参数统计包含匹配字符串的行数（**不是次数**）：    
    ```shell     
    $ echo -e "pattern1, pattern2\npaern03\nptern" | grep -c "pattern"
    1
    ```    
    显然由于只有第一行包含了两个"pattern"，于是输出为 1 。       

9. 使用 `wc` 命令统计匹配字符串出现的**次数**：     
    `wc` 命令的使用见 [一些常用的有用的linux命令 ](https://www.ouc-liux.cn/2021/05/07/Series-Article-of-UbuntuOS-04/#wc-%E5%91%BD%E4%BB%A4%E7%BB%9F%E8%AE%A1%E6%96%87%E4%BB%B6%E5%AD%97%E6%95%B0%E8%A1%8C%E6%95%B0%E5%AD%97%E8%8A%82%E6%95%B0)。    
    具体思路是使用 `-o` 参数将匹配到的字符串逐行打印为标准输出，并通过管道作为标准输入送如 `wc` 命令的执行序列：      
    ```shell    
    $ echo -e "1, 2. 3\n4,5\n6\n78" | grep -o -E "[0-9]" | wc -l    
    8
    ```     

10. 使用 `-n` 参数打印包含匹配字符串的行号：     
    ```shell    
    $ echo -e "line1\nline2\nline_3\nline_4" | grep -n _     
    3:line_3     
    4:line_4
    ```    

11. 使用 `-l` 参数寻找匹配文本位于哪一个文件：     
    ```shell    
    $ grep -l string sed_test1.txt grep_test*     
    sed_test1.txt     
    $ grep -l pattern sed_test1.txt grep_test*     
    grep_test1.txt     
    grep_test2.txt
    ```    

