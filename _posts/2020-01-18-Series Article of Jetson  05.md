---
layout:     post
title:      Series Article of Jetson -- 05
subtitle:   Jetson Nano 使用实录05 -- shouxieAI-tensorRT 框架编译和网络部署过程记录         
date:       2021-11-22
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Jetson Nano
---     

## 下载项目          
```
git clone https://github.com/shouxieai/tensorRT_Pro.git
```      

## 编译前配置      
###  protubuf      
一定要编译 3.11.4 版本，编译过程见[Linux 编译 protobuf](https://www.ouc-liux.cn/2021/11/02/Series-Article-of-Jetson-01/) 。     

### CMake      
在项目主路径找到 CMakeLists.txt 文件，需要面向 jetson nano 做一些具体的配置如下。不保证一下配置是最优配置，也不保证以下配置是唯一正确配置，但它可以运行。            
```sh
cmake_minimum_required(VERSION 2.6)
project(pro)
add_definitions(-std=c++11)

option(CUDA_USE_STATIC_CUDA_RUNTIME OFF)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_BUILD_TYPE Debug)
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/workspace)
set(HAS_PYTHON OFF) # 项目不需要 Python，关掉。       


# 如果要支持python则设置python路径
# set(PythonRoot "/data/datav/newbb/lean/anaconda3/envs/torch1.8")
# set(PythonName "python3.9")

# 如果你是不同显卡，请设置为显卡对应的号码参考这里：https://developer.nvidia.com/zh-cn/cuda-gpus#compute         
# jetson nano 的 gpu 算力为 53，填入即可。      
set(CUDA_GEN_CODE "-gencode=arch=compute_53,code=sm_53")

# 如果你的opencv找不到，可以自己指定目录         
# 编译 opencv 时指定的 prefix 是 /usr/local，但这里写填写 /usr/include 可以成功编译；换成 /usr/local/include/是否可以成功未知。      
set(OpenCV_DIR   "/usr/include")

set(CUDA_DIR     "/usr/local/cuda")
set(CUDNN_DIR    "/usr/")
set(TENSORRT_LIB "/usr/lib/aarch64-linux-gnu")
set(TENSORRT_INC "/usr/include/aarch64-linux-gnu")

# 因为protobuf，需要用特定版本，所以这里指定路径
set(PROTOBUF_INC "/usr/local/include")
set(PROTOBUF_LIB "/usr/local/lib")


find_package(CUDA REQUIRED)
find_package(OpenCV)

include_directories(
    ${PROJECT_SOURCE_DIR}/src
    ${PROJECT_SOURCE_DIR}/src/application
    ${PROJECT_SOURCE_DIR}/src/tensorRT
    ${PROJECT_SOURCE_DIR}/src/tensorRT/common
    ${OpenCV_INCLUDE_DIRS}
    ${CUDA_DIR}/include
    ${PROTOBUF_INC}
    ${TENSORRT_INC}
    ${CUDNN_DIR}/include
)

# 切记，protobuf的lib目录一定要比tensorRT目录前面，因为tensorRTlib下带有protobuf的so文件
# 这可能带来错误
link_directories(
    ${PROTOBUF_LIB}
    ${TENSORRT_LIB}
    ${CUDA_DIR}/lib64
    ${CUDNN_DIR}/lib
)

if("${HAS_PYTHON}" STREQUAL "ON")
    message("Usage Python ${PythonRoot}")
    include_directories(${PythonRoot}/include/${PythonName})
    link_directories(${PythonRoot}/lib)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DHAS_PYTHON")
endif()

set(CMAKE_CXX_FLAGS  "${CMAKE_CXX_FLAGS} -std=c++11 -Wall -O0 -Wfatal-errors -pthread -w -g")
set(CUDA_NVCC_FLAGS "${CUDA_NVCC_FLAGS} -std=c++11 -O0 -Xcompiler -fPIC -g -w ${CUDA_GEN_CODE}")
file(GLOB_RECURSE cpp_srcs ${PROJECT_SOURCE_DIR}/src/*.cpp)
file(GLOB_RECURSE cuda_srcs ${PROJECT_SOURCE_DIR}/src/*.cu)
cuda_add_library(plugin_list SHARED ${cuda_srcs})

add_executable(pro ${cpp_srcs})

# 如果提示插件找不到，请使用dlopen(xxx.so, NOW)的方式手动加载可以解决插件找不到问题
target_link_libraries(pro nvinfer nvinfer_plugin)
target_link_libraries(pro cuda cublas cudart cudnn)
target_link_libraries(pro protobuf pthread plugin_list)
target_link_libraries(pro ${OpenCV_LIBS})

if("${HAS_PYTHON}" STREQUAL "ON")
    set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/python/trtpy)
    add_library(trtpyc SHARED ${cpp_srcs})
    target_link_libraries(trtpyc nvinfer nvinfer_plugin)
    target_link_libraries(trtpyc cuda cublas cudart cudnn)
    target_link_libraries(trtpyc protobuf pthread plugin_list)
    target_link_libraries(trtpyc ${OpenCV_LIBS})
    target_link_libraries(trtpyc "${PythonName}")
    target_link_libraries(pro "${PythonName}")
endif()

add_custom_target(
    yolo
    DEPENDS pro
    WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}/workspace
    COMMAND ./pro yolo
)

add_custom_target(
    yolo_fast
    DEPENDS pro
    WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}/workspace
    COMMAND ./pro yolo_fast
)

add_custom_target(
    centernet
    DEPENDS pro
    WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}/workspace
    COMMAND ./pro centernet
)

add_custom_target(
    alphapose 
    DEPENDS pro
    WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}/workspace
    COMMAND ./pro alphapose
)

add_custom_target(
    retinaface
    DEPENDS pro
    WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}/workspace
    COMMAND ./pro retinaface
)

add_custom_target(
    dbface
    DEPENDS pro
    WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}/workspace
    COMMAND ./pro dbface
)

add_custom_target(
    arcface 
    DEPENDS pro
    WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}/workspace
    COMMAND ./pro arcface
)

add_custom_target(
    bert 
    DEPENDS pro
    WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}/workspace
    COMMAND ./pro bert
)

add_custom_target(
    fall
    DEPENDS pro
    WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}/workspace
    COMMAND ./pro fall_recognize
)

add_custom_target(
    scrfd
    DEPENDS pro
    WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}/workspace
    COMMAND ./pro scrfd
)

add_custom_target(
    lesson
    DEPENDS pro
    WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}/workspace
    COMMAND ./pro lesson
)

add_custom_target(
    pyscrfd
    DEPENDS trtpyc
    WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}/python
    COMMAND python test_scrfd.py
)

add_custom_target(
    pyinstall
    DEPENDS trtpyc
    WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}/python
    COMMAND python setup.py install
)

add_custom_target(
    pytorch
    DEPENDS trtpyc
    WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}/python
    COMMAND python test_torch.py
)

add_custom_target(
    pycenternet
    DEPENDS trtpyc
    WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}/python
    COMMAND python test_centernet.py
)
```       

### TensorRT 版本号           
最新的 jetson nano 系统镜像默认安装的是 tRT8.x 版本，项目默认支持的也是 8.x 。如果需要项目转换为 7.x 支持：    
```
bash onnx_parser/use_tensorrt_7.x.sh
```        
同理，如果需要项目转换为 7.x 支持：       
```
bash onnx_parser/use_tensorrt_8.x.sh：
```       

### Makefile         
不用动。如果（jetson nano）编译过程出现“找不到 -m64 指令”类似的错误，可以尝试将项目根路径 Makefile 中 74 行左右的 cu/cpp_compile_flags 两项中的 `-m64` 去掉，并按照上文中 CMakeFile.txt 的配置修改 Malefile，然后直接从 根路径 make。       
但，如果机器正常，项目也正常，不会出现错误。       

### cmake 以生成 MakeFile          
```bash        
mkdir build
cd build 
cmake ..
```

## yolov5 模型转为项目可接受的 onnx         
优先选择 v6.0 版本的 yolov5，效果好而且稳定。pytorch 版本应当1.8 及以上，低版本低了，可能会出错。onnx 需要 1.9.0 及以上，一般来说直接 `pip install onnx onnx-simplifier` 默认即可。        

### 训练     

正常训练，可以微调一下模型，不要大改源代码。       
### 针对导出 onnx 做的源码的改动          
主要是 `model/yolo.py` 和 `export.py`。         

```python       
# line 55 forward function in yolov5/models/yolo.py 
# bs, _, ny, nx = x[i].shape  # x(bs,255,20,20) to x(bs,3,20,20,85)
# x[i] = x[i].view(bs, self.na, self.no, ny, nx).permute(0, 1, 3, 4, 2).contiguous()
# modified into:

bs, _, ny, nx = x[i].shape  # x(bs,255,20,20) to x(bs,3,20,20,85)
bs = -1
ny = int(ny)
nx = int(nx)
x[i] = x[i].view(bs, self.na, self.no, ny, nx).permute(0, 1, 3, 4, 2).contiguous()

# line 70 in yolov5/models/yolo.py
#  z.append(y.view(bs, -1, self.no))
# modified into：
z.append(y.view(bs, self.na * ny * nx, self.no))

############# for yolov5-6.0 #####################
# line 65 in yolov5/models/yolo.py
# if self.grid[i].shape[2:4] != x[i].shape[2:4] or self.onnx_dynamic:
#    self.grid[i], self.anchor_grid[i] = self._make_grid(nx, ny, i)
# modified into:
if self.grid[i].shape[2:4] != x[i].shape[2:4] or self.onnx_dynamic:
    self.grid[i], self.anchor_grid[i] = self._make_grid(nx, ny, i)

    # disconnect for pytorch trace
    anchor_grid = (self.anchors[i].clone() * self.stride[i]).view(1, -1, 1, 1, 2)

# line 70 in yolov5/models/yolo.py
# y[..., 2:4] = (y[..., 2:4] * 2) ** 2 * self.anchor_grid[i]  # wh
# modified into:
y[..., 2:4] = (y[..., 2:4] * 2) ** 2 * anchor_grid  # wh

# line 73 in yolov5/models/yolo.py
# wh = (y[..., 2:4] * 2) ** 2 * self.anchor_grid[i]  # wh
# modified into:
wh = (y[..., 2:4] * 2) ** 2 * anchor_grid  # wh
############# for yolov5-6.0 #####################


# line 52 in yolov5/export.py
# torch.onnx.export(dynamic_axes={'images': {0: 'batch', 2: 'height', 3: 'width'},  # shape(1,3,640,640)
#                                'output': {0: 'batch', 1: 'anchors'}  # shape(1,25200,85)  修改为
# modified into:
torch.onnx.export(dynamic_axes={'images': {0: 'batch'},  # shape(1,3,640,640)
                                'output': {0: 'batch'}  # shape(1,25200,85) 

```         
这些改动只针对模型导出。建议是将这一份 `model/yolo.py` 保存为 `model/yolo_4_export.py`，训练的时候用正常的 `model/yolo.py` ，训练完要导出模型的时候：      
```bash     
mv model/yolo.py model/yolo_4_train.py      
mv model/yolo_4_export.py model/yolo.py      
```      

### 导出 .pt 为 .onnx         
需要保证yolov5 根目录下的 export.py 已经按照以上内容完成修改，需要保证 >=1.9.0 的 onnx 和 onnx-simplifier 已经通过 pip 正确安装。          
```bash             
python export.py --weights=/give/your/.pt/path --imgsz=416 --dynamic --opset=11 --include=onnx 
```           
由于默认的 img size 是 640，我们需要指定为自己训练的尺寸；--opset 要指定为 11，不知道为什么，但如果不这样做的话，会爆出一长串 wraning ； --include=onnx 指定只导出 onnx，防止混入其他奇奇怪怪的东西；--dynamic 不知道为什么加入，教程上这样做成功了。         

好了，现在可以把 onnx 模型 copy 到 jetson nano 板子上了。          

## 在板子上推理                      

假设现在已经完成了 onnx 模型转化和 jetson nano + shouxieAI 项目的配置。        

### onnx -> trtmodel        
编辑 `src/application/app_yolo.cpp` 文件：          
添加如下内容：        
```c++        
void onnx2trt(){
    INFO("Compile start.");
    TRT::Compile(
        TRT::Mode::FP16,    // FP32 or FP16, INT8 is not suggested      
        1,              // max batch size. if more than 1 image are 
                        // feed into engine each time, modify it.    
        "/path/to/onnx",    // path of onnx (imput) and trtmodel 
        "/path/to/trtmodel" // (output). Absolute path is required if
                            // in/out put models are not in workspace.    
    );
    INFO("Compile done");
}
```           

onnx 模型会从 “path/to/onnx” 读入，编译后的 trtmodel 模型会存储在 “path/to/trtmodel”。 如果输入输出模型不在项目根目录下的 workspace 路径，一定要使用绝对路径。相反的，如果不使用绝对路径，则默认从 workspace 读取、写入。             
模型可以选半精度 FP16，或全精度 FP32，具体效果和性能自己试验。       
参数第二项是 batch size ，我们设置为 1 ，这是由于我们一次性只喂入一张图片。如果有多路同步视频，需要一次性喂给引擎两张及以上的图片，按照实际情况设置。        

然后划到最下面 `app_yolo()` 函数，注释掉所有内容，只留一个      
```c++      
onnx2trt();
```    

回到 `build` 路径，执行：         
```bash         
make yolo -j3
```     

等很长一段时间，trt 模型就出来了。          

### 推理几张图片         

可以自己写一个推理函数，实际体验一下从 `Yolo::create_infer()` 到保存画好框的图像的过程，但没必要。可以直接调用 `app_yolo.cpp` 中写好的 `inference_and_performance()` 函数。          

在 `app_yolo.cpp` 添加如下内容：        
```c++       
void validation(){
    int deviceid = 0;   // jetson nano 没有多显卡，指定 0 即可         
    string model_file = "path/to/trtmodel"; // 如果模型不在 workspace 路径，
    // 要使用绝对路径
    TRT::Mode mode = TRT::Mode::FP16;   // 和导出的 trt 模型保持一致
    Yolo::Type type = Yolo::Type::V5;   // 如果训练的是 yolox，V5 换 X     
    const char* name = "your model";    // 给自己的模型取个名字。      
    inference_and_performance(
        deviceid,
        mode,
        type,
        name
    );
}
```           

还要微调一下 `inference_and_performance()` 函数的内容：          
```c++    
// engine define, row 27                   
auto engine = Yolo::create_infer(
    engine_file,    // 不用动，就是上面部分的 model_file           
    type,           // 不用动，就是上面部分的 type       
    deviceid,       // 不用动，就是上面部分的 deviceid           
    0.25f,          // 置信度阈值，自己调，建议不动，甚至可以低一点        
    0.45f,          // 非极大值抑制阈值，建议不动，动不动用处不明显        
    Yolo::NMSMethod::FaseGPU,   // FastGPU 可以换成 CPU，建议不动        
    1,              // 关键：一个图片里最多出现几个目标。之所以说上面置
                    // 信度阈值可以不用动甚至可以低一点，正是基于此。由于
                    // 我们的项目同一张图像里应当只出现一个饮料（目前是这
                    // 样），就强制置 1，nms 会自己选择置信度最大的那个目
                    // 标作为这个 1 。
    false,          // perprocess use multi stream。不知道什么意思，
                    // 不要动。          
)

// file path,row 42           
auto files = iLogger::find_files("/path/to/images", argv[...]);     
// 如果要测试的图片文件夹的路径不在 workspace，需要给出绝对路径           
```

依然，划到最下面 `app_yolo()` 函数，注释掉所有内容，只留一个      
```c++      
validation();
```    

回到 `build` 路径，执行：         
```bash         
make yolo -j3
```     

推理的结果在 workspace 一个名字里包含 `name` 的文件夹。      
完工。           

## 个性化定制        
自己按照 app_yolo.cpp 定制即可。回头有时间了再写个博客。      
