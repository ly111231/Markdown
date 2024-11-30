1、更改CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 2.8)
project(DBoW2)
# 提示用户是否设置 CROSS_COMPILE 参数
if(CROSS_COMPILE)
  message("1111111111")
  message(STATUS "CROSS_COMPILE is ON. Using cross-compilation toolchain.")
  #交叉编译时更改动态库输出路径
  set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib_arm64)
  set(OpenCV_DIR "/usr/local/opencv_aarch64/share/OpenCV")
else()
  message(STATUS "CROSS_COMPILE is OFF. Using native host compiler.")
  message("222222222")
  set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)
  set(OpenCV_DIR "/usr/local/share/OpenCV")
endif()

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS}  -Wall  -O3  ")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall  -O3 ")

set(HDRS_DBOW2
  DBoW2/BowVector.h
  DBoW2/FORB.h 
  DBoW2/FClass.h       
  DBoW2/FeatureVector.h
  DBoW2/ScoringObject.h   
  DBoW2/TemplatedVocabulary.h)
set(SRCS_DBOW2
  DBoW2/BowVector.cpp
  DBoW2/FORB.cpp      
  DBoW2/FeatureVector.cpp
  DBoW2/ScoringObject.cpp)

set(HDRS_DUTILS
  DUtils/Random.h
  DUtils/Timestamp.h)
set(SRCS_DUTILS
  DUtils/Random.cpp
  DUtils/Timestamp.cpp)

find_package(OpenCV 3.0 QUIET)
if(NOT OpenCV_FOUND)
   find_package(OpenCV 2.4.3 QUIET)
   if(NOT OpenCV_FOUND)
      message(FATAL_ERROR "OpenCV > 2.4.3 not found.")
   endif()
endif()


include_directories(${OpenCV_INCLUDE_DIRS})
add_library(DBoW2 SHARED ${SRCS_DBOW2} ${SRCS_DUTILS})
target_link_libraries(DBoW2 ${OpenCV_LIBS})

# 输出编译信息
message(STATUS "Using OpenCV version: ${OpenCV_VERSION}")
message(STATUS "OpenCV include directories: ${OpenCV_INCLUDE_DIRS}")
message(STATUS "OpenCV libraries: ${OpenCV_LIBS}")

# 输出交叉编译工具链信息
message(STATUS "Using C compiler: ${CMAKE_C_COMPILER}")
message(STATUS "Using C++ compiler: ${CMAKE_CXX_COMPILER}")
message(STATUS "Using OpenCV path: ${OpenCV_DIR}")
message(STATUS "LIBRARY_OUTPUT_PATH path: ${LIBRARY_OUTPUT_PATH}")

```

2、CmakeLists.txt 同级目录下创建toolchain.cmake文件，指定交叉编译器路径信息

```cmake
# toolchain.cmake

# 目标系统的名称和架构
SET(CMAKE_SYSTEM_NAME Linux)
SET(CMAKE_SYSTEM_PROCESSOR aarch64)

# 指定交叉编译器
SET(CMAKE_C_COMPILER /usr/local/toolchain/gcc-linaro-6.4.1-2017.11-x86_64_aarch64-linux-gnu/bin/aarch64-linux-gnu-gcc)
SET(CMAKE_CXX_COMPILER /usr/local/toolchain/gcc-linaro-6.4.1-2017.11-x86_64_aarch64-linux-gnu/bin/aarch64-linux-gnu-g++)
SET(CMAKE_LINKER /usr/local/toolchain/gcc-linaro-6.4.1-2017.11-x86_64_aarch64-linux-gnu/bin/aarch64-linux-gnu-ld)
SET(CMAKE_AR /usr/local/toolchain/gcc-linaro-6.4.1-2017.11-x86_64_aarch64-linux-gnu/bin/aarch64-linux-gnu-ar)

# 设置工具链的根路径（包含头文件和库文件）
SET(CMAKE_FIND_ROOT_PATH /usr/local/toolchain/gcc-linaro-6.4.1-2017.11-x86_64_aarch64-linux-gnu)

# 配置工具链查找路径
SET(CMAKE_FIND_ROOT_PATH_MODE_PROGRAM NEVER)
SET(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY ONLY)
SET(CMAKE_FIND_ROOT_PATH_MODE_INCLUDE ONLY)

# 指定目标架构
SET(CMAKE_C_FLAGS "-march=armv8-a")
SET(CMAKE_CXX_FLAGS "-march=armv8-a")

# 添加更多的 CMake 调试信息（可选）
message(STATUS "CMAKE_C_COMPILER: ${CMAKE_C_COMPILER}")
message(STATUS "CMAKE_CXX_COMPILER: ${CMAKE_CXX_COMPILER}")
message(STATUS "CMAKE_FIND_ROOT_PATH: ${CMAKE_FIND_ROOT_PATH}")
```

3、编译该库

```shell
mkdir build_cross
cd build_cross
cmake -D CMAKE_TOOLCHAIN_FILE=../toolchain.cmake -DCROSS_COMPILE=ON ..
make -j$(nproc) # 在lib_arm64目录生成libDBoW2.so共享库
```

