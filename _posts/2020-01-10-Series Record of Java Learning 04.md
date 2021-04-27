---
layout:     post
title:      Series Articles of Java Learning  -- 04
subtitle:   Java学习实录02 -- 总结一些小知识
date:       2021-01-31
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - 学习
    - Java
---

<head>
    <script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
    <script type="text/x-mathjax-config">
        MathJax.Hub.Config({
            tex2jax: {
            skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
            inlineMath: [['$','$']]
            }
        });
    </script>
</head>  


## List 和 ArrayList   
[Java项目实录02](https://www.ouc-liux.cn/2021/01/30/Series-Record-of-Java-Learning-02/)开头有这么一行代码：    
```java   
List<NameValuePair> params = new ArrayList<NameValuePair>();
```   
试图建立一个ArrayList（泛型）对象，再将之指向到List（泛型）。对该语句的正确解释是：   

List是一个接口，而ArrayList是List接口的一个实现类。   
ArrayList类是继承AbstractList抽象类和实现List接口的一个实现类。   
因此，List接口不能被构造，也就是我们说的不能创建实例对象，但是我们可以为List接口创建一个指向自己的对象引用，而ArrayList实现类的实例对象就在这充当了这个指向List接口的对象引用。  

我们进行一组对比：   
```java   
List list = new ArrayList();        // 1
List list = new List();             // 2
ArrayList alist = new ArrayList();  // 3
```    

上面三行代码中，**1** 是可行的用法，这个语句创建了一个ArrayList实现类的对象后把它上溯到了List接口。此时它就是一个List对象了，有些ArrayList类具有的，但是List接口没有的属性和方法，它就不能再用了。   
而**3** 创建的实现类ArrayList的对象则保留了ArrayList的所有属性和方法。   
**2** 是错误的写法，由于接口类List无法具体实现为对象，该句无法执行。    


## 正则表达式/去除字符串中特定值    

[项目实录02](https://www.ouc-liux.cn/2021/01/30/Series-Record-of-Java-Learning-02/)中遇到一个小问题：在一串由数字和短横杠组成的String类型变量中提取数字，得到一个纯数字字符串；问题可以等价为在原字符串中去除非数字字符。可以采用正则匹配方法处理，代码及解读如下：    
```java    
public static String IntegerOnly(String str){
    Pattern p = Pattern.compile("[^0-9]");  // 1
    Matcher m = p.matcher(str);             // 2
    return m.replaceAll("").trim();         // 3
}
```      

**语句 1** ：    
构造了一个正则表达式。`"[^0-9]"`是反向字符范围的正则表达，意思是除0--9以外的所有字符。由于0--9的ASICII码是连续的，从而可以通过短横连接作为一个范围。关于正则表达，相似的例子有：    
* `"[0-9]"` ：字符范围，意思是（以ASICII码顺序）0--9之间的字符。    
* `"[abc]"` ：字符集，意思是只允许abc三个字符。    
* `"[^abc]"`：反向字符集的，意思是除a、b、c以外的所有字符。   
* 关于正则表达的更多语法，见[这里](https://www.runoob.com/java/java-regular-expressions.html)。   

**语句 2** ：    
通过 1 构建的正则表达式对字符串str进行匹配，并返回一个带有匹配结果的`Matcher`变量。在本项目中就是匹配出有短横杠的String中的非数字字符。     

**语句 3** ：    
对语句 2 的匹配结果加以操作： 通过`Matcher`变量的`replaceAll(String)`方法将原始字符串str中的匹配结果（非数字字符）全部替换为空。该方法返回一个字符串，详见[文档](https://docs.oracle.com/javase/8/docs/api/java/util/regex/Matcher.html#replaceAll-java.lang.String-)。   
随后调用String变量的`trim()`方法，去除replaceAll之后的字符串头尾的空白符。得到纯数字组成的字符串,返回。     


## 类类型Double与基本数据类型double    
 
* Double是一个类，创建对象，存放在堆中   
* double是一种基本数据类型，创建引用，存放在栈中   
* Double是对double类型的封装，内置很多方法可以实现String到double的转换，以及获取各种double类型的属性值（MAX_VALUE，SIZE等）   
* `Double d =new Double(dou)`  可将double基本数据类型自动装箱为Double包装类    
* `double dou =d.doubleValue()`可将Double的包装类自动拆箱为基本数据类型    

Double提取字符串中浮点数的两个静态方法：    
* `Double.parseDouble(String)`： 把数字类型的字符串转换成基本数据类型double    
* `Double.valueOf(String)`： 把数字类型的字符串转换成类类型Double    


    
