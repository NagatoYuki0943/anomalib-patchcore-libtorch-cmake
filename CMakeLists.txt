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