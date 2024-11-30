1、创建toolchain.cmake

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

2、编译并安装到指定系统目录

```shell
mkdir build_cross
cd build_cross
cmake -D CMAKE_TOOLCHAIN_FILE=../toolchain.cmake - CMAKE_INSTALL_PREFIX=/usr/local/aarch64/eigen3_aarch64 ..
make -j$(nproc)
sudo make install
```

