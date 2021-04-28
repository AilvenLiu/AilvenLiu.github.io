---
layout:     post
title:      Series Articles of Java Learning  -- 02
subtitle:   Java项目实录02 -- 基于阿里云的普通增值税发票识别工具开发
date:       2021-03-31
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

>  写完崇教授的程序后，想趁着还熟悉开发流程，一些如GUI之类的组件也还能复用，写个发票识别工具。
>  这篇博客主要记录项目开发的流程，一些共性的问题和基本语法问题会在其他博客中详细记录。  

# 项目结构    
```    
Ticket2Excel    
|   
|--GenereteExcel        
|  |    
|  |--Generate    
|  |--Ticket2Excel     
|   
|--utils    
|  |     
|  |--Base64Utils     
|  |--HttpUtils     
|  |--PicUtils     
|  |--Utils    
|    
|--gui    
|  |--FileChooser    
```    

# 代码解析    
已经在[上一篇项目实录](https://www.ouc-liux.cn/2021/03/31/Series-Record-of-Java-Learning-01/)中介绍过的内容不再赘述，但会给出提示。    

## 主函数类： Ticket2Excel     

### main函数: main()    

`new Runnable(){...}`线程安全启动不再赘述。    

### 静态方法 submit2AliAPI(String, String)     

该方法内容大致可分为两部分，后半部分是结果解析，也就是怎么通过API返回的结果生成Excel表格，是解读的重点。前半部分主要是`http`实例构建、请求发送、参数设置、结果获取相关的内容。这些知识一是现阶段并不熟悉，二者也可能大部分都是固定的流程，本无需进行解读；但考虑到有些内容牵扯到其他知识点或者前后两部分的衔接，还是则其要者尝试解读，或许以后学习的更深入以后会对http部分进行补充。    

#### 第一部分--http    

定义适用于http传输的`NameValuePair`（来自包`org.apache.http`）链表变量：   
```java   
List<NameValuePair> params = new ArrayList<NameValuePair>();   
```   
关于接口类`List`和抽象类`AbstractList`的实现类`ArrayList`之间关系的小知识，见博客[java常见方法总结](https://www.ouc-liux.cn/2021/01/31/Series-Record-of-Java-Learning-04/)    

下一步，参照[这里](https://www.ouc-liux.cn/2021/03/31/Series-Record-of-Java-Learning-01/#%E9%9D%99%E6%80%81%E6%96%B9%E6%B3%95-submit2aliapi-string-imgpath-string-targetpath)关于图片`Base64`编码的知识对图片编码，并将相应的base64码构造为键值对加入到参数链表`params`中：   
```java    
params.add(new BasicNameValuePair("AI_VAT_INVOICE_IMAGE", imgBase64));
```    
此处注意的是`new BasicNameValuePair(key, value)`，同`List`与`ArrayList`之间的关系一样，`apache.http`中的`NameValuePair`定义了一种接口类型，它无法通过`new`具体实现。而`apache.http.message`中的`BasicNameValuePair`则是适用于这个接口的具体类。     

后面是 HttpGet 实例的构建和请求参数的设置，一是不熟，二是或许就是固定流程，不做解读。   

接下来是发送 HttpGet 请求，通过`httpClient.execute(httpPost)`实现。其中`httpPost`是上一步构建的`HttpPost`类型实例；`httpClient`被声明为一个`CloseableHttpClient`类型变量，但通过`HttpClient`（均来自`org.apache.http.impl.client`）的静态方法`createDefault()`定义。    
发送请求使用的`execute()`方法会返回一个`HttpResponse`类型变量，该变量（本项目设为`execute`）里包含着结果信息。比如可以通过`.getStatueLine().getStatueCode()`获取结果信息状态码已查看结果返回状态。该API中定义状态码为200为返回正常。    

在接下来就是获取结果信息。`execute.getEntity()`会返回一个`HttpEntity`类型变量，既是结果。但是这个结果对我们不是直观可读的，通过`EntityUtils`的静态方法`EntityUtils.parseString(HttpEntity)`可以将`HttpEntity`类型的结果转化为字符串类型。由于结果中包含多种信息，显然json对我们的处理更加方便。

JSON（来自`com.alibaba.fastjson`）中的静态方法`JSON.parseObject(String)`可以将字符串形式表示的结果转化为`JSONObject`变量`res_obj`。    


#### 第二部分--结果解析    

从上一步得到的JSONObject类型变量`res_obj`中将结果各部分提取出来，用到合适的位置。就本项目发票API而言：   
* 发票抬头是一个字符串变量，直接通过`res_obj.getString(key)`提取。    

* 发票日期是一个字符串变量，直接通过`res_obj.getString(key)`提取。   
不同的是，这个日期是“year-month-day”格式的字符串变量。我们不希望出现中间的小短横‘-’，于是通过`Utils`的静态方法`IntegerOnly()`进行提取结果为纯数字。由于涉及到一些有关正则匹配的共性知识，方法的具体实现在[java知识总结](https://www.ouc-liux.cn/2021/01/31/Series-Record-of-Java-Learning-04/#正则表达式去除字符串中特定值)中单独解读。       

* 发票总额是一个浮点型变量，所以通过`res_obj.getString(key)`提取出后，需要再通过`Double`的静态方法`Double.parseDouble(String)`将之转化为double型的 **基本数据类型** 变量。基本类型这一点很重要，`Double`还有一个静态方法`Double.valueOf(String)`返回一个Double型的 **类类型** 变量。关于Double 和 double的区别与联系依然见于[java知识总结](https://www.ouc-liux.cn/2021/01/31/Series-Record-of-Java-Learning-04/#类类型Double与基本数据类型double)中。    

* 发票detail是一个`JSONArray`变量，由多个具体条目组成，每个条目以`JSONObject`表现。通过`res_obj.getJSONArray(key)`提取。    

内容提取结束。然后定义一个String类型变量作为生成的Excel文件的名字，使用`String.format()`方法填充抬头、日期、总额等发票信息对该字符串变量赋值。

接下来创建一个`Generator()`对象，并调用其方法（不能是静态方法，原因存疑）`generator.generateExcel(invoice_detail, excel_name);`生成指定名字的excel表格。    

最后返回状态码`return stateCode;`结束该静态方法。      


## 类： Generator       

该类用于生成excel表格。处理excel表格需要使用`poi.hssf`库，通过      
