# 说明(Cmake版本)

> anomalib patchcore 的 torchscript C++推理
>
> 对应版本 pytorch-1.11_cu113 libtorch-1.11_cu113 cuda113



# anomalib地址

> https://github.com/NagatoYuki0943/anomalib

# 环境注意事项

## 依赖

- cuda
- cudnn
- libtorch
  - 注意版本要和pytorch一致，包括cuda版本
  - 分为Release和Debug版本，自己的项目也要匹配对应版本
- opencv
- rapidjson 读取json文件 https://github.com/Tencent/rapidjson
  - 将include文件夹下的rapidjson放到inclue目录下


## 环境变量

> `{opencv}`要替换为自己的对应的目录

```
{opencv}\build\x64\vc15\bin
{opencv}\build\x64\vc15\lib
{libtorch}\lib
```

> 注意：cuda和cudnn的环境变量在安装时就自动写入，这里不需要手动添加。

# Cmake

> 需要将opencv和pytorch导入

```cmake
cmake_minimum_required(VERSION 3.19)
project(CLion)

set(CMAKE_CXX_STANDARD 14)

# opencv
set(OPENCV_DIR D:/ai/opencv/build/x64/vc15/lib)
find_package(OpenCV REQUIRED)
if(OpenCV_FOUND)
    message(${OpenCV_INCLUDE_DIRS})
    message(${OpenCV_LIBRARIES})
endif()

# Torch
set(Torch_DIR D:/ai/libtorch/share/cmake/Torch)
find_package(Torch REQUIRED)
if (Torch_FOUND)
    message(${TORCH_INCLUDE_DIRS})  # D:/ai/libtorch/include D:/ai/libtorch/include/torch/csrc/api/include
    message(${TORCH_LIBRARIES})
endif ()
include_directories(${TORCH_INCLUDE_DIRS})

# 链接所有库，不指定cpp文件
link_libraries(${TORCH_LIBRARIES} ${OpenCV_LIBRARIES})  # 是 TORCH_LIBRARIES 不是 TORCH_LIBS

include_directories(include)

# add_executable写在下面
add_executable(main main.cpp opencv_utils.cpp utils.cpp)
```

# 注意问题

> vs中的相对目录就是cpp文件目录，而clion中的相对目录是`cmake-build-debug`和`cmake-build-release`目录

# 推理

> anomalib导出torchscript时可以选择cuda模式导出，根据网上的说法使用libtorch时调用显卡必须使用cuda导出模型，不过测试显示使用cpu导出的torchscript模型也能使用cuda推理

## anomalib导出

> https://github.com/NagatoYuki0943/anomalib
>
> 查看 anomalib 的 readme.md中的Export部分
>
> anomalib的export会导出模型和对应的超参数,超参数保存进了 `param.json` 中

## 推理

> 主文件在 `源.cpp`中
>
> 设置图片文件夹路径`imagedir`, 模型路径`model_path`和超参数路径`meta_path`进行推理.

# 问题

> anomalib中热力图的高斯模糊是在热力图和分数的标准化和放大热力图之前，使用的基于pytorch的conv2d，这里在放大图像之后，并且使用的opencv自带的高斯滤波，因为图片分辨率提高也因此增大了kernel_size，不过效果仍然没有python版本绘制的效果好



# opencv函数参考了mmdeploy的代码

`opencv_utils.cpp opencv_utils.h`

https://github.com/open-mmlab/mmdeploy/tree/master/csrc/mmdeploy/utils/opencv
