---
layout:     post
title:      Series Article of Deep Learning -- 01
subtitle:   目标检测01 -- 2020海智能船舶检测挑战赛实录    
date:       2021-05-07
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - pytorch
    - object detection
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

> 2020年首届智能船舶检测挑战赛使用yolov5获得研究生组三等奖，简单记录一下比赛过程。      

# 首先指出框架相关的问题     

我们使用了u版yolov5，改变网络结构比较方便。但即使自己改了更大的backbone，对数据进行了细致的清洗和花式的加噪曾广，对网络本身和检测方法竭尽所能添加tricks，最后也只是三等奖的水平。坤乾老师的学生原封不动使用scaled-yolov4，达到了一等奖的水平。这里对**yolov5的实际效果**表示严重怀疑。     

后来的一次水下目标检测任务，我测试了scaled-yolov4，效果的确比yolov5优秀。但也发现针对简单任务，**大模型的效果反而不如较小模型**，甚至超大模型还会出现loss报NaN的问题。     

# 比赛实录    

## 数据集    

所谓数据比赛，一半以上的成绩在于数据集的处理。     

### voc转yolo    

简单来说，VOC的xml格式图像和bbox尺寸是整数，该多大是多大，bbox通过角点坐标确定；yolo的规则中，bbox的长宽相对于图像长宽归一化到1.0以内，位置和尺寸通过中心点集宽高确定。转化的代码网上一找一大堆，但实际操作还是要结合任务的具体情况。最核心的部分就两个函数：     

```python    
def convert(size, box):
    dw = 1./(size[0])
    dh = 1./(size[1])
    x = (box[0] + box[1])/2.0 - 1
    y = (box[2] + box[3])/2.0 - 1
    w = box[1] - box[0]
    h = box[3] - box[2]
    x = x*dw
    w = w*dw
    y = y*dh
    h = h*dh
    if x>=1:
        x=0.999
    if y>=1:
        y=0.999
    if w>=1:
        w=0.999
    if h>=1:
        h=0.999
    return (x,y,w,h)

def convert_annotation(rootpath, xmlname):
    xmlpath = rootpath + '/xmls'
    xmlfile = os.path.join(xmlpath,xmlname)
    with open(xmlfile, "r", encoding='UTF-8') as in_file:
      txtname = xmlname[:-4]+'.txt'
      print(txtname)
      txtpath = rootpath + '/labels'  #生成的.txt文件会被保存在labels目录下
      if not os.path.exists(txtpath):
        os.makedirs(txtpath)
      txtfile = os.path.join(txtpath,txtname)
      with open(txtfile, "w+" ,encoding='UTF-8') as out_file:
        tree=ET.parse(in_file)
        root = tree.getroot()
        size = root.find('size')
        w = int(size.find('width').text)
        h = int(size.find('height').text)
        out_file.truncate()
        for obj in root.iter('object'):
            # difficult = obj.find('difficult').text
            cls = obj.find('name').text
            if cls not in classes: 
            # if cls not in classes or int(difficult)==1:
                continue
            cls_id = classes.index(cls)
            xmlbox = obj.find('bndbox')
            b = (float(xmlbox.find('xmin').text), \
                float(xmlbox.find('xmax').text), \
                float(xmlbox.find('ymin').text), \
                float(xmlbox.find('ymax').text))
            objWidth = b[1]-b[0]
            objHeight = b[3]-b[2]
            if objWidth > w or objHeight > h or objWidth*objHeight<=21:
                continue
            bb = convert((w,h), b)
            out_file.write(str(cls_id) + " " + " ".join([str(a) \
                for a in bb]) + '\n')

```     

这东西就是轮子，轮子怎么造的就不做解读了，主要是会用。指出几个需要注意的点：    
1. `txtpath = rootpath + '/labels'`这一行，`labels`路径需要自己事先创建，不会自动创建。     
2. `difficult = obj.find('difficult').text`这一句，比赛数据集没有`difficult`项，事实上，我遇到的大多数数据集都没有这一项，直接注释掉即可。     
3. 相应地，后面条件语句里面的`int(difficult)==1`这一判断也得删掉，只留一个`if cls not in classes`。    

