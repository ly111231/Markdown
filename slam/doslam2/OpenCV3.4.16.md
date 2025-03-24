## 安装OpenCV 3.4.16

### 安装相关库

```shell
sudo apt-get install build-essential libgtk2.0-dev pkg-config
```

1. **`build-essential`**：这个包包含了编译工具（如 `gcc`、`g++` 和 `make` 等），它是编译任何源代码所必需的基本工具。
2. **`libgtk2.0-dev`**：这个包提供了 GTK 2.0 图形界面库的开发文件，用于支持 OpenCV 的 GUI 功能（例如显示图像窗口）。即使你不涉及复杂的图形界面功能，安装它可以帮助你显示处理后的图像。
3. **`pkg-config`**：这个工具用于查找和配置库的路径，确保在编译过程中正确链接 OpenCV 所需的库。

### 编译OpenCV库

选择“开发板直接编译OpenCV库” 或者 “本地交叉编译”后再“拷贝到开发板”

#### 开发板直接编译OpenCV库

```shell
mkdir build
cd build
cmake  -D CMAKE_INSTALL_PREFIX=/usr/local/opencv_aarch64 -D WITH_QT=ON -D WITH_GTK=ON -D WITH_OPENGL=ON  -DCMAKE_CXX_FLAGS="-w" .. #-DCMAKE_CXX_FLAGS="-w" 忽略警告，不然g++版本高语法严格会报错
make -j$(npro) # 在build目录中构建opencv动态库
sudo make install # 将build目录下的动态库拷贝到指定路径/usr/local/opencv_aarch64
# 忽略以下
cmake -D CMAKE_BUILD_TYPE=Release \
      -D CMAKE_INSTALL_PREFIX=/usr/local \
      -D OPENCV_EXTRA_MODULES_PATH=~/opencv_contrib-3.4.16/modules \
      -D BUILD_TESTS=OFF \
      -D INSTALL_PYTHON_EXAMPLES=OFF \
      -D WITH_QT=ON -D WITH_GTK=ON -D WITH_OPENGL=ON \
      -D BUILD_EXAMPLES=OFF ..
```



#### 本地交叉编译

1、安装交叉编译工具链 gcc-linaro-6.4.1-2017.11-x86_64_aarch64-linux-gnu

2、在OpenCV源码目录下创建toolchain.cmake指配置交叉编译器路径

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

3、构建OpenCV动态库

```shell
mkdir build_cross
cd build_cross
cmake -D CMAKE_TOOLCHAIN_FILE=../toolchain.cmake -D CMAKE_INSTALL_PREFIX=/usr/local/opencv_aarch64 -D WITH_QT=ON -D WITH_GTK=ON -D WITH_OPENGL=ON  ..
make -j$(npro)
sudo make install 
```



#### 拷贝到开发板

将编译好的OpenCV动态库拷贝到开发板的`/usr/local/opencv_aarch64`路径下

### 添加路径

```shell
#将编译好的opencv动态库路径添加到 系统的动态链接器搜索路径中
sudo echo "/usr/local/opencv_aarch64/lib" > /etc/ld.so.conf.d/opencv_aarch64.conf #切换到root执行
sudo ldconfig #刷新系统的库缓
```

```shell
# 设置 PKG_CONFIG_PATH 让 pkg-config 知道 .pc (opencv.pc)文件的路径。
echo "export PKG_CONFIG_PATH=/usr/local/opencv_aarch64/lib/pkgconfig:$PKG_CONFIG_PATH" >> ~/.bashrc
source ~/.bashrc
pkg-config --modversion opencv # 查看opencv版本
g++ -o a a.cpp $(pkg-config --cflags --libs opencv) # 可这样使用opencv动态库
```

cmake -D CMAKE_TOOLCHAIN_FILE=../toolchain.cmake -DCROSS_COMPILE=ON ..

sudo scp -r ubuntu@192.168.137.222:/lib /usr/local/toolchain/gcc-linaro-6.4.1-2017.11-x86_64_aarch64-linux-gnu/sysroot/

sudo scp -r ubuntu@192.168.137.222:/usr/include /usr/local/toolchain/gcc-linaro-6.4.1-2017.11-x86_64_aarch64-linux-gnu/sysroot/usr/

sudo scp -r ubuntu@192.168.137.222:/usr/lib /usr/local/toolchain/gcc-linaro-6.4.1-2017.11-x86_64_aarch64-linux-gnu/sysroot/usr/

sudo tar -czvf /tmp/usr_lib.tar.gz /usr/lib #压缩

sudo tar -xzvf /tmp/usr_lib.tar.gz -C /usr/local/toolchain/gcc-linaro-6.4.1-2017.11-x86_64_aarch64-linux-gnu/sysroot/usr #解压

sudo scp -r ubuntu@192.168.137.222:/tmp/usr_lib.tar.gz /tmp/usr_lib.tar.gz

![image-20241129093519727](https://lypicbed.oss-cn-beijing.aliyuncs.com/Markdown/202411290935815.png)

cmake -D CMAKE_TOOLCHAIN_FILE=../toolchain.cmake -DCROSS_COMPILE=ON ..
