---
layout:     post
title:      Series Article of Jetson -- 05
subtitle:   Jetson Nano 使用实录05 -- shouxieAI-tensorRT 框架编译和网络部署问题记录         
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

###