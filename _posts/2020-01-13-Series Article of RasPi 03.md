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
3. yolo 根目录下运行命令：     
    ```bash       
    $ export PYTHONPATH="$PWD"  python models/export.py --weights path/of/weights.pt --img image_shape --batch 1         
    ```      
    这里注意，执行 python 命令之前是否要加 `export PYTHONPATH="$PWD"` 语句临时指定python 路径，各个技术博客意见不一。我的实验结果是要加，不加会报错。具体细节并没有深究。      

4. 成功的标志是 terminal 打印出 
   ```     
   ONNX export success, saved as ./weights.onnx      
   ```       
   如果不成功，根据报错信息继续调整。       


### onnx --> openvino     
OpenVino 的模型文件叫IR，实际上分为三个文件：`weights.bin`, `weights.mapping`, `weights.xml`，应该是 bin 文件存储了权重， xml 文件存储了模型图，mapping 就不知道了，似乎也没用到。      
win + visual studio 安装 OpenVino 的话，需要装一个 cmake。装吧，早晚用得到的。需要 visual studio 2019 版本。OpenVino 版本简易且强烈建议同 Raspi 上的版本保持一致。其余的，按照 OpenVino 官网关于 win 安装的[教程](https://docs.openvinotoolkit.org/latest/openvino_docs_install_guides_installing_openvino_windows.html)来就好了。如果出现问题，再看一遍教程，要仔细看。      
安装好，就可以转换模型了。      

1. 将 onnx 模型 copy 到 win 机器上。      
2. 打开 cmd ，如果在 conda 环境中安装的 OpenVino，进入到相应 conda 环境；如果裸奔，此一步可略过。     
3. 启动环境。进入到 `path/of/IntelSWTools/openvino` 路径，执行命令：     
   ```cmd     
   > bin\\setupvars.bat     
   ```    
   于是 openvino 安装成功切环境成功启动成功的标志是 cmd 打印出：     
   ```cmd     
   Python <version>       
   [setupvars.bat] OpenVINO environment initialized       
   ```      
   这里的子路径 `openvino` 实际上是同级自路径 `openvino_<version>` 的软连接，所以进入这两个自路径是等效的。      
4. 安装依赖。从上一步继续进入到 `deployment_tools/model_optimize/` 自路径    
   ```cmd     
   > pip install -r requirements_onnx.txt      
   ```     
   以上三步一般不会出问题。如果出问题，google 解决吧，我没出，也不知道会遇到什么问题。     
5. 模型转化。保持上一步的路径不变，执行代码：      
   ```cmd      
   > python mo.py --input_model path/to/weights.onnx --output_dir path/to/output/ --input_shape [1,3,shape,shape] --data_type FP16     
   ```     
   建议就半精度 FP16，尝试 INT8 的时候好像是报错还是最终结果起飞，反正试过，没用。    
   转换成功会给信息，且 `path/to/output` 路径下会出现 `weights.bin`, `weights.mapping`, `weights.xml` 三个文件。     


## 模型测试      

以下是在 RasPi + NCS2 上实际部署时的代码，runnable。     
```python    
from __future__ import print_function

import logging as log
import os
import time
# time.sleep(20)

import pathlib
import json
import cv2
import numpy as np
from openvino.inference_engine import IENetwork, IECore
import torch
import torchvision
import os
import serial
import math
import shutil

font = cv2.FONT_HERSHEY_SIMPLEX


def xywh2xyxy(x):
    """
    processing with numpy-ndarray is required, for segmentation 
    fault will be caused when using torch tensor.
    """

    # Convert nx4 boxes from [x, y, w, h] to [x1, y1, x2, y2] where xy1=top-left, xy2=bottom-right

    # y = torch.zeros_like(x) if isinstance(
    
    #     x, torch.Tensor) else np.zeros_like(x)
    
    x=x.numpy()
    y=np.zeros_like(x)
    y[:, 0] = x[:, 0] - x[:, 2] / 2.  # top left x
    
    y[:, 1] = x[:, 1] - x[:, 3] / 2.  # top left y
    
    y[:, 2] = x[:, 0] + x[:, 2] / 2.  # bottom right x
    
    y[:, 3] = x[:, 1] + x[:, 3] / 2.  # bottom right y
    
    return torch.from_numpy(y)


def non_max_suppression(prediction, conf_thres=0.1, iou_thres=0.6, \
                        classes=None, agnostic=False):
    """Performs Non-Maximum Suppression (NMS) on inference results

    Returns:
         detections with shape: nx6 (x1, y1, x2, y2, conf, cls)
    """
    
    prediction = torch.from_numpy(prediction)
    # print(prediction.shape)

    if prediction.dtype is torch.float16:
        prediction = prediction.float()  # to FP32


    nc = prediction[0].shape[1] - 5  # number of classes

    xc = prediction[..., 4] > conf_thres  # candidates

    # Settings

    # (pixels) minimum and maximum box width and height

    min_wh, max_wh = 2, 4096
    max_det = 300  # maximum number of detections per image

    time_limit = 10.0  # seconds to quit after

    redundant = True  # require redundant detections

    multi_label = nc > 1  # multiple labels per box (adds 0.5ms/img)


    t = time.time()
    output = [0] * prediction.shape[0]
    for xi, x in enumerate(prediction):  # image index, image inference

        # Apply constraints

        # x[((x[..., 2:4] < min_wh) | (x[..., 2:4] > max_wh)).any(1), 4] = 0  # width-height
        
        x = x[xc[xi]]  # confidence

        # If none remain process next image
        
        if not x.shape[0]:
            continue

        # Compute conf
        
        x[:, 5:] *= x[:, 4:5]  # conf = obj_conf * cls_conf


        # !!!!!!!!!!!!!!!!!

        # !!! BUG BELOW !!!

        # !!!!!!!!!!!!!!!!!

        # Box (center x, center y, width, height) to (x1, y1, x2, y2)

        box = xywh2xyxy(x[:, :4]) # torch tensor is forbidded

        # Detections matrix nx6 (xyxy, conf, cls)

        if multi_label:
            i, j = (x[:, 5:] > conf_thres).nonzero(as_tuple=False).T
            x = torch.cat((box[i], x[i, j + 5, None], j[:, None].float()), 1)
        else:  # best class only

            conf, j = x[:, 5:].max(1, keepdim=True)
            x = torch.cat((box, conf, j.float()), 1)[
                conf.view(-1) > conf_thres]

        # Filter by class

        if classes:
            x = x[(x[:, 5:6] == torch.tensor(classes, device=x.device)).any(1)]

        # Apply finite constraint

        # if not torch.isfinite(x).all():

        #     x = x[torch.isfinite(x).all(1)]


        # If none remain process next image

        n = x.shape[0]  # number of boxes

        if not n:
            continue

        # Sort by confidence

        x = x[x[:, 4].argsort(descending=True)]
        
        # Batched NMS

        c = x[:, 5:6] * (0 if agnostic else max_wh)  # classes

        # boxes (offset by class), scores

        boxes, scores = x[:, :4] + c, x[:, 4]
        i = torchvision.ops.boxes.nms(boxes, scores, iou_thres)
        if i.shape[0] > max_det:  # limit detections

            i = i[:max_det]

        output[xi] = x[i]
        if (time.time() - t) > time_limit:
            break  # time limit exceeded


    return output


device = 'MYRIAD'
# device = 'CPU'

input_h, input_w, input_c, input_n = (192, 192, 3, 1)
log.basicConfig(level=log.DEBUG)

# For objection detection task, replace your target labels here.

label_id_map = ["circle", 'square']
exec_net = None


def init(model_xml):
    if not os.path.isfile(model_xml):
        log.error(f'{model_xml} does not exist')
        return None
    model_bin = pathlib.Path(model_xml).with_suffix('.bin').as_posix()
    net = IENetwork(model=model_xml, weights=model_bin)

    ie = IECore()
    global exec_net
    exec_net = ie.load_network(network=net, device_name=device)
    log.info('Device info:')
    versions = ie.get_versions(device)
    print("{}".format(device))
    print("MKLDNNPlugin version ......... {}.{}".format(versions[device].major, versions[device].minor))
    print("Build ........... {}".format(versions[device].build_number))

    input_blob = next(iter(net.inputs))
    n, c, h, w = net.inputs[input_blob].shape
    global input_h, input_w, input_c, input_n
    input_h, input_w, input_c, input_n = h, w, c, n

    return net


def process_image(net, input_image):
    if not net or input_image is None:
        log.error('Invalid input args')
        return None
    ih, iw, _ = input_image.shape

    if ih != input_h or iw != input_w:
        input_image = cv2.resize(input_image, (input_w, input_h))
    input_image = cv2.cvtColor(input_image, cv2.COLOR_BGR2RGB)
    input_image = input_image/255
    input_image = input_image.transpose((2, 0, 1))
    images = np.ndarray(shape=(input_n, input_c, input_h, input_w))
    images[0] = input_image

    input_blob = next(iter(net.inputs))
    out_blob = next(iter(net.outputs))

    start = time.time()
    res = exec_net.infer(inputs={input_blob: images})
    end = time.time()
    log.info('inference time: {}ms'.format(int((end - start)*1000)))

    data = res[out_blob]

    data = non_max_suppression(data, 0.85, 0.2)
    # log.info('nms finished')

    # data = data[0]

    # print(type(data[0]))

    detect_objs = []
    # log.info(data[0])

    if type(data[0]) == int:
        return 0
    # if len(data) == 0:

    #     return 0

    else:
        data = data[0].numpy()
        for proposal in data:
            if proposal[4] > 0:
                xmin = np.int(iw * (proposal[0]/192))
                ymin = np.int(ih * (proposal[1]/192))
                xmax = np.int(iw * (proposal[2]/192))
                ymax = np.int(ih * (proposal[3]/192))
                confidence = proposal[4]
                detect_objs.append({
                    'xmin': int(xmin),
                    'ymin': int(ymin),
                    'xmax': int(xmax),
                    'ymax': int(ymax),
                    'confidence': float(confidence),
                    'name': label_id_map[int(proposal[5])]
                })

        return detect_objs


def plot_bboxes(image, bboxes, line_thickness=None):
    # Plots one bounding box on image img

    tl = line_thickness or round(
        0.002 * (image.shape[0] + image.shape[1]) / 2) + 1  # line/font thickness

    for box in bboxes:
        x1,x2,y1,y2 = box['xmin'], box['xmax'], box['ymin'],box['ymax']
        conf, name = box['confidence'], box['name']
        if name == 'circle':
            color = (0, 0, 255)
        else:
            color = (0, 255, 0)
        c1, c2 = (x1, y1), (x2, y2)
        cv2.rectangle(image, c1, c2, color, thickness=tl, lineType=cv2.LINE_AA)
        tf = max(tl - 1, 1)  # font thickness

        t_size = cv2.getTextSize(name, 0, fontScale=tl / 3, thickness=tf)[0]
        c2 = c1[0] + t_size[0], c1[1] - t_size[1] - 3
        cv2.rectangle(image, c1, c2, color, -1, cv2.LINE_AA)  # filled

        cv2.putText(image, '{} conf-{}'.format(name, conf), (c1[0], c1[1] - 2), 0, tl / 3,
                    [225, 255, 255], thickness=tf, lineType=cv2.LINE_AA)

    return image


if __name__ == '__main__':
    main()
```      