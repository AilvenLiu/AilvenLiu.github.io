---
layout:     post
title:      Series Articles of Java Learning  -- 01
subtitle:   Java项目实录01--基于阿里云的表格识别OCR工具开发
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

>  受崇教授所托，开发一款图片转表格OCR工具。决定调用阿里云的api，使用其java接口进行二次开发。项目代码开源在[github](https://github.com/OUCliuxiang/Java_related/tree/main/AliTableOCR)。  
>  这篇博客主要记录项目开发的流程，一些共性的问题和基本语法问题会在其他博客中详细记录。  



# 项目结构：    
```
AliTableOCR   
|  
|--data   
|--lib     
|--out    
|--src    
|  |--com    
|  |  |--alibaba.ocr.damo    
|  |  |  |--AliTableOCR  
|  |  |  |--Base64excel  
|  |  |   
|  |  |--aliyun.api.gateway.demo.util  
|  |  |  |--HttpUtils  
|  |  |  |--pom.xml  
|  |  |  
|  |  |--gui   
|  |  |  |--FileChooser   
|  
|--jars...
```

# 代码解析    
## 类 AliTableOCR   
### 方法 main()
```java   
public static void main(String[] args) {
    EventQueue.invokeLater(new Runnable() {
        @Override
        public void run() {
            FileChooser.createWindow();
        }
    });
}
```   
由于`class AliTableOCR`的所有方法映射都到了GUI界面，从而项目`main`函数只有一个功能：启动GUI界面。并，通过`EventQueue.invokeLater(Runnable)`实现接口(Inference)`Runnable`中的`run()`方法保证`main`的线程安全。  

### 方法 submit2AliAPI( String imgPath, String targetPath)      

将图片编码为base64提交到阿里云API，并将返回的json数据解析为excel表格。   
```
Params:   
@ imgPath:      接收图片路径    
@ targetPath:   目标表格路径
```   

需要避免IO错误，但为查看实际解析出来的excel表格内容，此处不采取异常捕获机制，而是给`targetExcel`一个初始值。方便起见，目标表格路径需要和输入图片路径一一对应。由于输入图片限制为.jpg和.png格式，可以通过简单的字符串`replace()`方法实现，如66-69行代码：   
```java
else if (imgName.charAt(length-2)=='N') {
    targetExcel = targetPath + '\\' + imgName.replace(".PNG", ".xlsx");
}
```    
API的输入接口为base64编码字符串，则需要将目标图片编码为base64：      
```java
String imgBase64 = "";
try {
    File file = new File(imgFile);
    byte[] content = new byte[(int) file.length()];
    FileInputStream finputstream = new FileInputStream(file);
    finputstream.read(content);
    finputstream.close();
    imgBase64 = new String(encodeBase64(content));
} catch (IOException e) {
    e.printStackTrace();
    return;
}
```
通过`try-catch`语句捕获输入输出异常。    
通过`new FileInputStream(file)`创建文件`file = new File(path)`的输入流，并通过`read(byte[])`方法将文件流中的字节返回到字节数组中，字节数组的长度依照输入图片文件file而定。这是由于`encondBase64(byte[])`方法给出的输入接口是字节数组。    
不要忘记关闭文件输入流。    

下一步（row 114--）