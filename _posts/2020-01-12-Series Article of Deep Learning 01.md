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

> 2020年首届智能船舶检测挑战赛使用yolov5获得研究生组三等奖，简单记一些目标检测任务的小知识和比赛tricks。      

# 首先指出框架相关的问题     

我们使用了u版yolov5，改变网络结构比较方便。但即使自己改了更大的backbone，对数据进行了细致的清洗和花式的加噪曾广，对网络本身和检测方法竭尽所能添加tricks，最后也只是三等奖的水平。坤乾老师的学生原封不动使用scaled-yolov4，达到了一等奖的水平。这里对**yolov5的实际效果**表示严重怀疑。     

后来的一次水下目标检测任务，我测试了scaled-yolov4，效果的确比yolov5优秀。但也发现针对简单任务，**大模型的效果反而不如较小模型**，甚至超大模型还会出现loss报NaN的问题。     

# 比赛实录    

## 数据集    

所谓数据比赛，一半以上的成绩在于数据集的处理。     

### voc转yolo（小目标清洗）    

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

这东西就是轮子，轮子怎么造的不做解读了，主要是会用。指出几个需要注意的点：    
1. `txtpath = rootpath + '/labels'`这一行，`labels`路径需要自己事先创建，不会自动创建。     
2. `difficult = obj.find('difficult').text`这一句，比赛数据集没有`difficult`项，事实上，我遇到的大多数数据集都没有这一项，直接注释掉即可。     
3. 相应地，后面条件语句里面的`int(difficult)==1`这一判断也得删掉，只留一个`if cls not in classes`。    
4. 有些数据集，比如这次比赛用的数据集，标注是有问题的。存在很多误标的ground truth，猜测是标注数据的学生手一抖，就点了俩点儿，成了一个ground truth。这种情况可以在读xml文件的时候通过bbox和weight/hight把长宽/像素值或其相对整体图像的比例小于某个值的目标筛掉，不进入转yolo的过程。     

具体的实现上，如果已有的VOC数据集本身已经分好组了（在数据比赛中，这应该是大多数情况），则遍历train和a_test路径调用函数即可。     


### 数据集划分    
考虑一般情况下，数据比赛给出的a_test只是用来打榜，自己还是需要另外分一个val验证集用以在训练过程中监控网络状态。下面给出已将数据按yolo要求组织后，划分数据集的代码。      

```python    
valImg = "xxx"      # 将要被划分出来的验证集图片路径      
trainImg = "xxx"    # 目前图片路径     
valLabel = "xxx"    # 将要被划分出来的验证集标签路径
trainLabel = "xxx"  # 当前标签路径      

files = os.listdir(trainImg)
for f in files:
    if random.random()<0.25:       # 这个比例按需求来
        shutil.move(trainImg+f, valImg+f)
        shutil.move(trainLabel+f.replace('jpg', 'txt'), 
                    valLabel+f.replace('jpg', 'txt'))    
```     

### 负样本和空标签     

比赛中去除了所有空标签，因为我们认为数据足够多、样本足够丰富，负样本的存在没有必要。但另一项简单的水下目标检测的任务中，由于目标和场景过于简单，且有些背景和目标过与相似，则需要添加相应的负样本。所谓负样本，就是对应于某图片的标签文件存在但为空。    

#### 删除空标签    

通过python读文件操作判断文件是否为空，遍历标签文件直接删除空标签文件。参照[这篇博客](https://www.ouc-liux.cn/2021/05/07/Series-Article-of-Python-Using-01/#%E5%88%A4%E6%96%AD%E6%96%87%E4%BB%B6%E6%98%AF%E5%90%A6%E4%B8%BA%E7%A9%BA)。    

#### 添加负样本     

如果是自己收集的图片，则遍历没有标签的背景图片，并通过[系统命令](https://www.ouc-liux.cn/2021/05/07/Series-Article-of-Python-Using-01/#%E8%B0%83%E7%94%A8%E7%B3%BB%E7%BB%9F%E5%91%BD%E4%BB%A4)`touch`生成相应的空标签：     
```python    
files = os.listdir("./background/")     
for f in files:    
    label = f.replace("jpg", "txt")   
    os.system("touch trainLabel/" + label)    
```     

需要注意的是，执行这一步的时候应该先行筛选出一定数量和形式的负样本图片，并加入到数据集。   

### 添加噪声          

给清洗完毕的数据添加噪声是一种非常有效的涨点方法。比赛总共使用了三种噪声和两种添加方式，为原图片添加了五类噪声，简单介绍如下：     

**三种噪声**：     
* 椒盐噪声：由 0(0.0) 和 255(1.0) 两个值组成的噪声。噪点在彩色RGB三通道图像中的位置可以相同，也可以不同。      
* 高斯噪声：符合高斯分布的噪声。     
* 叠加噪声：将另一幅图片的（部分）像素值按一定比例和权重叠加到目标图片。         

**两种添加方式**：      
* 像素相加：全部噪声像素和输入图片按不同权重直接相加，超过255的部分直接砍掉。        
* 像素替换：按一定的比例将原始图片中部分像素直接替换为噪声像素。          

添加噪声的代码如[github/smartShip2020/data/dataReformance.py](https://github.com/OUCliuxiang/smartShip2020/blob/main/data/dataReinformance.py)所示。需要注意的一个点儿是，添加噪声对时间序列没有要求，可以调用python多进程工具进行并行处理，可以极大地加快处理速度。