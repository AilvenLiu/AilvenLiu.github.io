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

