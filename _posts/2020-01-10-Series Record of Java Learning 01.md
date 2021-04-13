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

### 静态方法 submit2AliAPI( String imgPath, String targetPath)      

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

下一步（row 114--134）将图片编码联合http头文件、请求、配置等等内容拼接成为json文件变量并通过`JSONObject().toString()`转化为字符串变量，目的是提交给云API。JSONObject变量可以通过`json.put(key, body)`加入JSONArray变量，其中JSONArray通过`JSONArray.add(JSONObject)`添加json变量为json数组。

接下来提交数据到云API并接收网络响应HttpResponse：   
```java
HttpResponse response = HttpUtils.doPost(host, path, method, headers, querys, bodys);    
```    
response的状态码通过`response.getStatusLine().getStatusCode()`获取；主体内容通过`response.getEntity()`获取，通过http工具中的`EntityUtils.toString(entity)`得到相应的字符串数据。通过JSON类的静态方法`JSON.parseObject(res)`将上一步获得的字符串数据转化为JSONObject数据，这一步的目的是为了获取到主体内容中特定的value值，也即tables值。 该字符串数据是一串base64编码，通过**Base64excel**类中的 *convert2excel(String base64code, String targetPath)* 方法可以将之转化为目标excel文件： 
```java
String base64code = res_obj.getString(key="tables");    
Base64excel.convert2excel(base64code, targetExcel);
```    

至此，单张图片转化为excel表格的核心功能完成。     


## 类 Base64Excel    

无需第三方jar包，该类只需要导入`java.io` 和 `java.util.Base64`即可。   

### 静态方法 convert2excel(String base64, String output)    

```
params
@base64:    接收到云API返回的base64编码    
@output:    输出的excel路径    
```

通过Base64包中的静态方法`Base64.getDecoder()`创建`Base64.Decoder`类对象。   
定义并初始化文件输出流对象`OutputStream out = null`用以写入数据。   
将文件输出流对象指向到**output**文件，此处有可能发生`FileNotFoundException`异常，使用try-catch捕获。    
写入数据：     
```java
out.write(decoder.decode(base64));    
```

## 类 FielChooser: GUI界面     

东西南北中五部分的`Borderlayout`界面，界面表现如图所示：   
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/javaSeries/java-005.png"></div>    

### 静态方法 creatWindow()    

用于窗口布局：    

```java    
JFrame frame = new JFrame("图片转表格OCR");
frame.setLayout(new BorderLayout());
frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

createNorthPanel();
frame.add(northPanel, BorderLayout.NORTH);

createSouthPanel();
frame.add(southPanel, BorderLayout.SOUTH);

createCenterPanel();
frame.add(centerPanel, BorderLayout.CENTER);

frame.setSize(500, 400);
frame.setResizable(true);
frame.setLocationRelativeTo(null);
frame.setVisible(true);
```   

### 静态方法 createNorthPanel()   

窗口上方区域，用于选择/给定输入输出路径。   
基本的提升GUI用户体验的选择是预先填充一个路径到路径文本输入框，而不是一个空的路径文本输入框。这一操作通过获取当前文件的绝对路径而实现：    
```java
File directory = new File("");     
path = directory.getAbsolutePath();      
```      
显而易见的是，该区域( panel)应当分为两行三列： 输入一行，输出一行；Label（也即文本框前面的提示）一列，文本框一列，再加一个选择按钮，可以通过鼠标点击而不是键盘键入的方式填充路径选择文本框。于是：   
```java
northPanel = new Panel(new GridLayout(2, 3));   
```    
为保证观感，我们希望标签在第一列应当靠右，文本框在中间一列应当靠左：    
```java   
northPanel.add(new JLabel("输入路径：", SwingConstants.RIGHT));   
northPanel.add(picPath, BorderLayout.WEST);   
```    

按钮通过静态方法*createButton(String name, JTextField textField)* 构造。   

### 静态方法 createButton(String name, JTextField textField)   
