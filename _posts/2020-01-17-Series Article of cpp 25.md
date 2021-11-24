---
layout:     post
title:      Series Article of cpp -- 25
subtitle:   二维 vector 排序 / sort 排序库函数使用自定义排序规则            
date:       2021-11-24
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:     
    - c++   
    - STL
---     

sort()函数，默认的是对二维数组按照第一列的大小对每行的数组进行排序。所以可以加上cmp函数用按照任意列对数组进行排序。         
```c++
#include<bits/stdc++.h>
using namespace std;
//按照二维数组第一列的大小对每个一维数组升序排序，        
//如何第一列相同时，按照第二列大小对每行的数组 降序 排序        
bool cmp(vector<int>&a,vector<int>&b){
    if(a[0]!=b[0]) return a[0]<b[0];
    else return a[1]>b[1];
}
int main()
{
    vector<vector<int> >a(6);
    int x;
    for(int i=0;i<6;i++){
        for(int j=0;j<2;j++){
            cin>>x;
            a[i].push_back(x);
        }
    }
    cout<<endl;
    sort(a.begin(),a.end(),cmp);
    for(int i=0;i<6;i++){
        for(int j=0;j<2;j++){
            cout<<a[i][j]<<" ";
        }
        cout<<endl;
    }
    return 0;
}
```