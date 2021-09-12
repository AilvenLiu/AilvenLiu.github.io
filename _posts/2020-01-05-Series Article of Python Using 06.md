---
layout:     post
title:      Series Article of Python Using -- 06
subtitle:   Python实录06 -- 字典排序         
date:       2021-09-12
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - python   
---
     
## 按键(Key)排序     

```python    
def dictionairy():    
    # 声明字典      

    key_value ={}     
 
    # 初始化
    
    key_value[2] = 56       
    key_value[1] = 2 
    key_value[5] = 12 
    key_value[4] = 24
    key_value[6] = 18      
    key_value[3] = 323 
 
    print ("按键(key)排序:")   
 
    # sorted(key_value) 返回重新排序的列表
    
    # 字典按键排序
    
    for i in sorted (key_value) : 
        print ((i, key_value[i]), end =" ") 
  
def main(): 
    # 调用函数
    
    dictionairy()              
      
# 主函数

if __name__=="__main__":      
    main()
```      
得到输出为：     
```     
按键(key)排序:    
(1, 2) (2, 56) (3, 323) (4, 24) (5, 12) (6, 18) 
```      
注意，`sorted(key_value)` 得到的只有键，1,2,3,4,5,6 等。    


## 按值(Value)排序     
```python     
def dictionairy():  
    # 声明字典

    key_value ={}     
 
    # 初始化
    
    key_value[2] = 56       
    key_value[1] = 2 
    key_value[5] = 12 
    key_value[4] = 24
    key_value[6] = 18      
    key_value[3] = 323 
 
 
    print ("按值(value)排序:")   
    print(sorted(key_value.items(), key = lambda kv:kv[1]))     
   
def main(): 
    dictionairy()             
      
if __name__=="__main__":       
    main()
```     
得到输出：     
```python    
按值(value)排序:
[(1, 2), (5, 12), (6, 18), (4, 24), (2, 56), (3, 323)]
```       
注意， `key_value.items()` 以二元组列表形式返回整个字典；lambda 表达式的意思是给进一个参数 `kv` (实际上是输入列表的一个二元组元素)，返回二元组的第二项（value），于是以该 `lambda` 表达是作为 `sorted()` 高阶函数的排序关键值，就成了按照字典的值排序。      

## 字典列表排序    

知道了 `sorted()` 高阶函数和 `lambda` 表达式的配合，既可以进行字典列表排序。    

```python    
lis = [{ "name" : "Taobao", "age" : 100},  
       { "name" : "Runoob", "age" : 7 }, 
       { "name" : "Google", "age" : 100 }, 
       { "name" : "Wiki" , "age" : 200 }] 
  
# 通过 age 升序排序      

print ("列表通过 age 升序排序: ")
print (sorted(lis, key = lambda i: i['age']) )
  
print ("\r") 
  
# 先按 age 排序，再按 name 排序      

print ("列表通过 age 和 name 排序: ")
print (sorted(lis, key = lambda i: (i['age'], i['name'])) )
  
print ("\r") 
  
# 按 age 降序排序      

print ("列表通过 age 降序排序: ")
print (sorted(lis, key = lambda i: i['age'],reverse=True) )

```     

得到输出：    
```python     
列表通过 age 升序排序: 
[{'name': 'Runoob', 'age': 7}, {'name': 'Taobao', 'age': 100}, {'name': 'Google', 'age': 100}, {'name': 'Wiki', 'age': 200}]

列表通过 age 和 name 排序: 
[{'name': 'Runoob', 'age': 7}, {'name': 'Google', 'age': 100}, {'name': 'Taobao', 'age': 100}, {'name': 'Wiki', 'age': 200}]

列表通过 age 降序排序: 
[{'name': 'Wiki', 'age': 200}, {'name': 'Taobao', 'age': 100}, {'name': 'Google', 'age': 100}, {'name': 'Runoob', 'age': 7}]
```