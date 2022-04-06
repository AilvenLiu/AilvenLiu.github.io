---
layout:     post
title:      Linux C/C++ 学习笔记 -- 01          
subtitle:   getopt 和 atoi 方法解析命令行参数               
date:       2022-04-06
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Linux C/C++ Programming     
    - WebServer
--- 

`getopt()` 方法是用来分析命令行参数的，该方法由 Unix 标准库提供，包含在 `unistd.h` 头文件中，定义为：        
```c++
int getopt(int argc, char * const argv[], const char *optstring);
```         
由于 `getopt（）` 方法接收到的参数类型均是字符串，其常常与字符转整型方法 `atoi()` 配合使用，后者包含在 `stdlib.h` 库中。             

**参数说明**：        
* `argc`：通常由 main 函数直接传入，表示参数的数量           
* `argv`：通常也由 main 函数直接传入，表示参数的字符串变量数组           
* `optstring`：一个包含正确的参数选项字符串，用于参数的解析。例如 “ab:c::”，其中        
  -a 后面没有冒号，代表普通的无参数选项；         
  -b 后面有一个冒号，是必须有参数的选项，且其参数和选项之间可以有空格隔开，也可以没有；          
  -c 后面有两个冒号，代表一个可选参数的选项，也即其参数可有可无。但是如果后面跟参数，参数和选项之间不允许空格，否则会报错。          

当方法调用失败，返回 -1。           


### 实例代码             

给出一段实际使用的代码片段如下：          

```c++
void Config::parse_arg(int argc, char* argv[]){
    int opt;
    const char *str = "p:l:m:o:s:t:c:a:";
    while ((opt = getopt(argc, argv, str)) != -1)
    {
        switch (opt)
        {
        case 'p':
        {
            PORT = atoi(optarg);
            break;
        }
        case 'l':
        {
            LOGWrite = atoi(optarg);
            break;
        }
        ... ...
        default:
            break;
        }
    }
}
```

