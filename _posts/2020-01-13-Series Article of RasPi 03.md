---
layout:     post
title:      Series Article of RasPi -- 03
subtitle:   树莓派使用实录03 -- NCS2部署yolov5模型              
date:       2021-07-30
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - RasPi   
    - pytorch    
    - object detection    
---     

> 这是按照2021年4月份实践经验整理出来的博客，由于 pytorch, yolov5, openvino 等版本的更新，现在可能已经不适用。但如果遇到问题，那问题形式应该也大差不差。        


首先，备好两个pytorch(gpu) 版本，一个应当是 1.7.1 以上，用来执行 yolov5 网络训练与推理测试。一个严格为 1.5.1 版本，这是由于高版本 torch 似乎会在安装（使用） onnx 的过程中出现问题。       

## 修改模型      

由于YOLOv5的许多算子openvino仍然不支持，因此我们需要做出几点修改。主要是将所有的激活函数（ `Hardswish`， `swish`， `Mish`等）全部替换为 `ReLU` 或 `LeakyReLU`，为避免遗漏，建议使用 grep 搜索。       

参照 [grep 文本搜索和 sed 文本替换](https://www.ouc-liux.cn/2021/07/21/Series-Article-of-UbuntuOS-09/)，使用 `grep "act" *.py -n` 对2021年3月份的 yolov5/models 子路径进行激活函数检索，发现有如下地方需要修改：    
1. `common.py`:line:40;       
2. `common.py`:line:89;        
3. `export.py`:line:47;      

而依照 CSDN 上的[【玩转YOLOv5】YOLOv5转openvino并进行部署](https://blog.csdn.net/weixin_44936889/article/details/110940322) 这篇博客，更老一个版本的 yolov5 有如下地方需要修改：      
1. models/yolo.py：      
   ```python    
   self.act = nn.Hardswish() if act else nn.Identity() # 删掉，修改为：        

   self.act = nn.LeakyReLU(0.1, inplace=True) if act else nn.Identity()         
   ```     

2. models/export.py       
   ```python      
   if isinstance(m, models.common.Conv) and isinstance(m.act, nn.Hardswish):       
            m.act = Hardswish()              
    #  删掉，修改为：       
    if isinstance(m, models.common.Conv) and isinstance(m.act, nn.LeakyReLU):      
            m.act = LeakyReLU()      
    ```              

3. utils/torch_utils.py        
   ```python     
   elif t in [nn.Hardswish, nn.LeakyReLU, nn.ReLU, nn.ReLU6]:        
   #删掉， 修改为：      
   elif t in [nn.LeakyReLU, nn.LeakyReLU, nn.ReLU, nn.ReLU6]:      
   ```

如果实在不放心的话，比较笨和保险的方法是在 models/ 和 utils/ 自路径下使用 grep 逐个搜索 `Hardsiwsh`, `Swish`, `Mish` 等关键词，然后一一替换。      
事实上，较新版本的 yolov5 中需要修改的部分已经不多了。     

## 模型训练      

使用修改完的 yolov5 在 pytorch1.7.1+ 的环境训练模型。事实上，也可以在训练的时候不使用修改版，仅在 pt --> onnx 过程使用修改版，但这样做或许会造成精度下降，所以，还是老老实实用比较好。      

另一个不确定但值得注意的点是 `models/yolo.py` 中的 `Detect` 层。按照 [yolov5_openvino_SDK](https://github.com/linhaoqi027/yolov5_openvino_sdk) 的说法，需要将 `models/yolo.py` 中第 49--53 行的 forward 方法种几行语句作如下修改：     
```python     
    y[..., 0:2] = (y[..., 0:2] * 2. - 0.5 + self.grid[i].to(        
        x[i].device)) * self.stride[i]  # xy       
    y[..., 2:4] = (y[..., 2:4] * 2) ** 2 * self.anchor_grid[i]  # wh      
    z.append(y.view(bs, -1, self.no))       
return x if self.training else (torch.cat(z, 1), x)      
```     
改为：    

```python     
    c = (y[..., 0:2] * 2. - 0.5 + self.grid[i].to(x[i].device)) *      
        self.stride[i]  # xy        
    d = (y[..., 2:4] * 2) ** 2 * self.anchor_grid[i]  # wh       
    e = y[..., 4:]      
    f = torch.cat((c,d,e),4)       
    z.append(f.view(bs, -1, self.no))      
return x if self.training else torch.cat(z, 1)
```     
由于时间久远，目前已经忘记是否有必要对之做出更改、应该在 training 阶段还是只在 pt -onnx 阶段，以及更改的作用何在。应该是有必要修改的，因为码农家园的 [基于YoloV5的模型优化技术与使用OpenVINO推理实现](https://www.codenong.com/cs109702222/) 这篇博客也提到了 `yolo.py` 里这部分的修改。       
但注意到这几行语句的改变只改变 inference 阶段的输出，而无关 training 阶段，故而直接改变在training-inference 全阶段应该没有影响。而且注意到 [YOLOv5转openvino并进行部署](https://blog.csdn.net/weixin_44936889/article/details/110940322) 这篇博客的评论区提到了部署时 bbox 可以正确标出，但 confidence 报负数的问题，而我的部署后期莫名解决了相应问题，故这个改变或许和 confidence 数值有关。      
依然，由于时代久远，更具体的，需要实际部署实验验证。      

训练，得到 `best.pt` 模型权重文件。        


## 模型转换        

模型转换需要两步， pt --> onnx 和 onnx --> openvino。这两步虽然麻烦，但只要注意以下两点，应该不会有意外：     
1. pt --> onnx 步骤需要且严格需要在 **torch==1.5.1, torchvision==0.6.1, onnx==1.7, opset=10** 版本下进行。        
2. onnx --> openvino 步骤，虽然按照 [基于YoloV5的模型优化技术与使用OpenVINO推理实现](https://www.codenong.com/cs109702222/)  可以在 ubuntu 下进行，个人实践中却不成功。建议在 win + visual studio 下进行这一步，保成功。     

### pt -- onnx      

1. 进入 torch1.5.1 环境；     
2. 修改 `models/export.py`：     
   ```python     
   torch.onnx.export(model, img, f, verbose=False,       
        opset_version=11, input_names=['data'],      
        output_names=['prob']if y is None else ['output'])       
    ```       
    version 参数改为 10，这是因为版本为 11 的 opset 不支持 resize 算子，直接将 11 版本的算子库转到 openvino 会报错。      
    此外，[yolov5_openvino_SDK](https://github.com/linhaoqi027/yolov5_openvino_sdk) 还提出将        
    ```python      
    model.model[-1].export = True       
    ```     
    中的布尔值改为 `False`。      
    [基于YoloV5的模型优化技术与使用OpenVINO推理实现](https://www.codenong.com/cs109702222/) 提出将 `torch.onnx.export` 中的 output_names 参数由 `['prob']if y is None else ['output']` 改为 `['classes', 'boxes'] if y is None else ['output']`。      
    以上，此外提出的两点修改自己没有执行，但似乎影响不大。如果 export 过程出现问题或感觉效果不好，可以试着修改。     
    

