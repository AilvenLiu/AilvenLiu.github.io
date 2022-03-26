---
layout:     post
title:      Series Article of Algorithm and Data Structure -- 05 
subtitle:   KMP 算法 （背过即可）    
date:       2022-03-26
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Algorithm      
--- 

> from ACWing [830. KMP字符串](https://www.acwing.com/problem/content/833/)， [bilibili-KMP视频讲解](https://www.bilibili.com/video/BV1My4y1V74Y?p=12P12) 第12P，1小时28分开始。          


KMP 的核心思想在于模式串的 next 数组。模式串中每一个字符对应一个 next 数组元素。next 数组存储截止到当前字符再前一个字符的最大相同前后缀长度。         
举例子，截止到当前字符之前的子串为 "abcabc"，则其 next 值为 3。需要注意，前后缀不能是子串整体，比如对子串 "aaaaa" ，其前后缀最长取到 "aaaa"，从而 next 值为 4 。        
同时也应当注意到，每个位置的 next 位置也是最长匹配前缀后面的位置。         

### next 数组生成（背过即可）            

0 位置和 1 位置的值人为指定为 -1 和 0。给出一个 cn = 0 代表当前位置前一位置（1 位置）对应的 next 值。从 2 位置开始：             
1. 若前一个位置字符和 cn 位置（最初代表前一位置 next 指向位置）的字符相同，则说明前一个位置最长匹配前后缀各自在往后延展一个字符，仍然是匹配的。即可 next[i++] = ++cn;           
2. 否则，若 cn 非零， cn = next[cn]; 这一步，不可言传，画个图就能悟到。背过即可。      
3. 再否则，前一位置截止的字串没有能相同的前后缀，next[i] = 0 即可。      

```c++
char p[N];
int next[N];

void getNext(int n){
    // n: lenth of pattern string.              
    next[0] = -1;
    next[1] = 0;
    int cn = 0, i = 2;
    while(i < n){
        if (p[i-1] == p[cn])    next[i++] = ++cn;
        else if (cn)            cn = next[cn];
        else if                 next[i++] = 0;
    }
}
```
      
### 模式匹配（背过即可）               
模式串和匹配串从零开始。       
1. 当前元素相等，各自后移一位；            
2. 不相等，模式串下标回退到当前位置的 next 位置。这一步，可意会不可言传，画个图模拟一下立刻明白。背过即可。               
3. 若模式串顺利走完，匹配成功，返回 j-n 也即匹配串成功匹配到模式串头的位置即可。             
4. 若匹配串中包含匹配串不止一次，模式串下标回退到尾位置对应 next 位置，相应的，匹配串下标也要移动到和上一次匹配到匹配串尾对应的位置。画个图，立刻悟道。背过即可。                          

```c++
const int M = 1e6+10, N = 1e5+10;
char s[M], p[N];
int next[N];

void getNext(int n){
    pass;
}

int main(){
    int m, n;   scanf("%d%d", &m, &n);
    for (int i = 0; i < m; i++) scanf("%c", &s[i]);
    for (int i = 0; i < n; i++) scanf("%c", &p[i]);

    getNext(n);

    for ( int i = 0, j = 0; i < n && j < m; ){
        if (p[i] == s[j])   i++, j++;
        else                i = next[i];

        if (i == n){
            printf("%d ", j-n);
            j--;
            i = next[n-1];
        }
    }
}
```












