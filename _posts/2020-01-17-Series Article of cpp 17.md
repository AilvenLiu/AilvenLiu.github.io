---
layout:     post
title:      Series Article of cpp -- 17
subtitle:   for 循环里不要添加非截止条件的判断语句          
date:       2021-10-28
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:     
    - c++   
---     

for 循环的第二项是循环终止条件，如果添加了 if 语句，一旦 if 不成立，循环break不再继续。      
```c++
for( ; i<n && if ...; i){
    ；
}
```          
如上面代码，当 if 判断为 false ，不是 continue 而是 break 。