---
layout:     post
title:      Series Article of cpp -- 04
subtitle:   uint8_t / uint16_t / uint32_t /uint64_t 是什么数据类型      
date:       2021-10-01
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:     
    - c++ 
---     

> 刷 leetcode 位操作遇到了 `uint32_t` ，不清楚这是个什么新的数据类型，找了几篇博客，讲解不错，搬运如下。      

一般来说，一个 C 的工程中一定要做一些这方面的工作，因为你会涉及到跨平台，不同的平台会有不同的字长，所以利用预编译和 typedef 可以让你最有效的维护你的代码。为了用户的方便，C99 标准的 C 语言硬件为我们定义了这些类型，我们放心使用就可以了。 按照 posix 标准，一般整形对应的 `*_t` 类型为：
```
1字节     uint8_t       
2字节     uint16_t      
4字节     uint32_t       
8字节     uint64_t      
```

这些数据类型是 C99 中定义的，具体定义在：`/usr/include/stdint.h`：         
```c           
/* There is some amount of overlap with <sys/types.h> as known by inet code */  
#ifndef __int8_t_defined  
# define __int8_t_defined  
typedef signed char             int8_t;   
typedef short int               int16_t;  
typedef int                     int32_t;  
# if __WORDSIZE == 64  
typedef long int                int64_t;  
# else  
__extension__  
typedef long long int           int64_t;  
# endif  
#endif  
  
/* Unsigned.  */  
typedef unsigned char           uint8_t;  
typedef unsigned short int      uint16_t;  
#ifndef __uint32_t_defined  
typedef unsigned int            uint32_t;  
# define __uint32_t_defined  
#endif  
#if __WORDSIZE == 64  
typedef unsigned long int       uint64_t;  
#else  
__extension__  
typedef unsigned long long int  uint64_t;  
```          

可是我找了找自己机器（Ubuntu 18.04.3 LTS, 64-bit）上的 `/usr/include/stdint.h` ，内容是这样的：        
```c     
/* Small types.  */

/* Signed.  */
typedef signed char		int_least8_t;
typedef short int		int_least16_t;
typedef int			int_least32_t;
#if __WORDSIZE == 64
typedef long int		int_least64_t;
#else
__extension__
typedef long long int		int_least64_t;
#endif

/* Unsigned.  */
typedef unsigned char		uint_least8_t;
typedef unsigned short int	uint_least16_t;
typedef unsigned int		uint_least32_t;
#if __WORDSIZE == 64
typedef unsigned long int	uint_least64_t;
#else
__extension__
typedef unsigned long long int	uint_least64_t;
#endif


/* Fast types.  */

/* Signed.  */
typedef signed char		int_fast8_t;
#if __WORDSIZE == 64
typedef long int		int_fast16_t;
typedef long int		int_fast32_t;
typedef long int		int_fast64_t;
#else
typedef int			int_fast16_t;
typedef int			int_fast32_t;
__extension__
typedef long long int		int_fast64_t;
#endif

/* Unsigned.  */
typedef unsigned char		uint_fast8_t;
#if __WORDSIZE == 64
typedef unsigned long int	uint_fast16_t;
typedef unsigned long int	uint_fast32_t;
typedef unsigned long int	uint_fast64_t;
#else
typedef unsigned int		uint_fast16_t;
typedef unsigned int		uint_fast32_t;
__extension__
typedef unsigned long long int	uint_fast64_t;
#endif
```         

这？？？     
算了，有时间再探究原因吧。暂且先记下形如 `uint32_t` 的数据类型就是为统一不同平台基本数据类型长度而定义的别名。           

注意，必须小心 `uint8_t` 类型变量的输出，例如如下代码，会输出什么呢？         
```c++
uint8_t fieldID = 67;
cerr<< "field=" << fieldID <<endl;
```
结果发现是：field=C 而不是我们所想的 field=67。这是由于:       
```c
typedef unsigned char uint8_t;
```
`uint8_t` 实际是一个 char, cerr << 会输出 ASCII 码是 67 的字符，而不是 67 这个数字。因此，输出 `uint8_t` 类型的变量实际输出的是其对应的字符, 而不是真实数字。       
若要输出 67,则可以这样：         
```c++
cerr<< "field=" << (uint16_t) fieldID <<endl;
```
结果是：field=67          
同样： `uint8_t` 类型变量转化为字符串以及字符串转化为 `uint8_t` 类型变量都要注意, `uint8_t` 类型变量转化为字符串时得到的会是 ASCII 码对应的字符, 字符串转化为 `uint8_t` 变量时, 会将字符串的第一个字符赋值给变量。       