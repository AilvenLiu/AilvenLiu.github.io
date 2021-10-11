---
layout:     post
title:      Series Article of cpp -- 09
subtitle:   How to sum up elements of a C++ vector?        
date:       2021-10-10
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:     
    - c++     
    - STL      
---     
> from [StackOverflow](https://stackoverflow.com/questions/3221812/how-to-sum-up-elements-of-a-c-vector) 。          
         
### C++03          
C++03

Classic for loop:
```c++
for(std::vector<int>::iterator it = vector.begin(); it != vector.end(); ++it)
    sum_of_elems += *it;
```          

Using a standard algorithm:
```c++
#include <numeric>
sum_of_elems = std::accumulate(vector.begin(), vector.end(), 0);
```            

Important Note: The last argument's type is used not just for the initial value, but for the type of the result as well. If you put an int there, it will accumulate ints even if the vector has float. If you are summing floating-point numbers, change 0 to 0.0 or 0.0f (thanks to nneonneo). See also the C++11 solution below.          

### C++11 and higher

2.b. Automatically keeping track of the vector type even in case of future changes:
```c++
#include <numeric>
sum_of_elems = std::accumulate(vector.begin(), vector.end(),
                                decltype(vector)::value_type(0));
```         

Using std::for_each:          
```c++
std::for_each(vector.begin(), vector.end(), [&] (int n) {
    sum_of_elems += n;
});
```            

Using a range-based for loop (thanks to Roger Pate):         
```c++
for (auto& n : vector)
    sum_of_elems += n;
```           
