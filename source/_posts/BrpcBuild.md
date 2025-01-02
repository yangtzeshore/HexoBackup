---
title: brpc编译环境搭建
date: 2025-01-02 14:20:30
tags:
- brpc CXX
categories:
- CXX

---

众所周知，C++编译是个大难题，之前的brpc环境学习环境是放在公司电脑的，现在在自己电脑的虚机Ubuntu16.04上，重新搭建个学习环境。以下是艰苦奋战的结果。



## 升级 `CMake` 

以下是升级 `CMake` 的详细步骤：

------

### **1. 检查当前版本**

先确认当前系统已安装的 `CMake` 版本：

```bash
cmake --version
```

------

### **2. 从官方网站下载最新版本**

访问 [CMake 官方下载页面](https://cmake.org/download/) 选择适合你的系统版本（通常是 `Linux x86_64` 的 tar.gz 文件）。

或者使用命令下载最新的稳定版本：

```bash
wget https://github.com/Kitware/CMake/releases/latest/download/cmake-<version>-linux-x86_64.sh
```

> **注意**：将 `<version>` 替换为所需的版本号，例如 `3.27.6`。

------

### **3. 安装前准备**

赋予下载的安装文件执行权限：

```bash
chmod +x cmake-<version>-linux-x86_64.sh
```

------

### **4. 安装 CMake**

运行安装脚本：

```bash
sudo ./cmake-<version>-linux-x86_64.sh --prefix=/usr/local --skip-license
```

#### 参数解释：

- `--prefix=/usr/local`: 安装路径，默认是 `/usr/local`。
- `--skip-license`: 跳过显示许可证的确认。

------

### **5. 验证安装**

验证新版本是否成功安装：

```bash
cmake --version
```

------

### **6. 替换旧版本（如果必要）**

如果仍然显示旧版本的 `CMake`，可能是系统路径未更新导致。通过以下命令更新路径：

#### 检查当前的 CMake 路径：

```bash
which cmake
```

#### 更新系统路径：

```bash
sudo ln -sf /usr/local/bin/cmake /usr/bin/cmake
```

再次验证版本：

```bash
cmake --version
```

------

### **7. 清理临时文件**

安装完成后，可以删除下载的安装包：

```bash
rm -f cmake-<version>-linux-x86_64.sh
```

------



## 安装 `autoreconf` 工具

安装Protobuf前，需要安装 `autoreconf` 工具。

------

#### 1. **安装 `autoconf` 和相关工具**

根据操作系统安装所需的软件包：

##### **Ubuntu/Debian**

```bash
sudo apt-get update
sudo apt-get install -y autoconf automake libtool
```

##### **CentOS/RHEL**

```bash
sudo yum install -y autoconf automake libtool
```

##### **Fedora**

```bash
sudo dnf install -y autoconf automake libtool
```

##### **MacOS (Homebrew)**

```bash
brew install autoconf automake libtool
```





## 源码安装Protobuf

安装 Protobuf（Protocol Buffers）有多种方式，可以根据操作系统和具体需求选择适合的方法。以下是安装 Protobuf 的详细步骤，包括通过包管理器、源码安装以及其他方法。

------

### 1. **通过包管理器安装**

#### **Ubuntu/Debian**

```bash
sudo apt-get update
sudo apt-get install -y protobuf-compiler libprotobuf-dev
```

#### **CentOS/RHEL**

```bash
sudo yum install -y protobuf protobuf-devel
```

#### **Fedora**

```bash
sudo dnf install -y protobuf protobuf-devel
```

#### **MacOS (Homebrew)**

```bash
brew install protobuf
```

通过包管理器安装是最快捷的方法，但版本可能较旧。如果需要特定版本，请选择源码安装。

------

### 2. **从源码安装**

#### **步骤**

1. **下载源码** 前往 [Protobuf Releases 页面](https://github.com/protocolbuffers/protobuf/releases)，选择需要的版本，下载源码包或克隆仓库：

   ```bash
   git clone https://github.com/protocolbuffers/protobuf.git
   cd protobuf
   git checkout v3.6.0 # 或者指定其他版本
   ```

2. **构建和安装** 确保安装了构建工具（如 `cmake` 或 `autotools`）：

   - 使用 `autotools`（推荐）

     ```bash
     ./autogen.sh
     ./configure
     make -j$(nproc)
     sudo make install
     sudo ldconfig
     ```

   - 使用 `cmake`

     ```bash
     mkdir build
     cd build
     cmake .. -DCMAKE_BUILD_TYPE=Release
     make -j$(nproc)
     sudo make install
     sudo ldconfig
     ```

3. **验证安装** 检查 `protoc` 的版本：

   ```bash
   protoc --version
   ```

------

### 3. **通过预编译的二进制文件安装**

如果不想自己构建，可以使用官方提供的预编译二进制文件：

1. 下载对应系统的二进制包：[Protobuf Releases](https://github.com/protocolbuffers/protobuf/releases)

2. 解压：

   ```bash
   tar -xvf protobuf-all-<version>.tar.gz
   ```

3. 将二进制文件移动到系统路径：

   ```bash
   sudo mv bin/protoc /usr/local/bin/
   sudo chmod +x /usr/local/bin/protoc
   ```

4. **验证**

   ```bash
   protoc --version
   ```

------

### 4. **安装到特定目录（可选）**

如果需要将 Protobuf 安装到自定义路径，例如 `/opt/protobuf`，可以在 `./configure` 或 `cmake` 时指定路径：

```bash
./configure --prefix=/opt/protobuf
make -j$(nproc)
make install
```

然后将路径添加到环境变量中：

```bash
export PATH=/opt/protobuf/bin:$PATH
export LD_LIBRARY_PATH=/opt/protobuf/lib:$LD_LIBRARY_PATH
```

------

### 5. **常见问题**

#### **找不到 Protobuf 的头文件或库**

确保 Protobuf 的 `include` 和 `lib` 路径已经正确配置：

```bash
export CXXFLAGS="-I/usr/local/include"
export LDFLAGS="-L/usr/local/lib"
```

#### **Protobuf 版本不兼容**

如果依赖项目对 Protobuf 有版本要求，建议使用源码安装并选择特定版本。

#### **构建速度慢**

可以使用 `make -j$(nproc)`，利用多核加速编译。

------



## 源码安装 `gflags`

以下是从源码安装 `gflags` 的完整步骤：

### **0. 卸载旧的 gflags**

如果你之前安装了 gflags，可以先清除它：

```
sudo rm -rf /usr/local/lib/libgflags.*
sudo rm -rf /usr/include/gflags
```

------

### **1. 安装必要的工具和依赖**

确保你的系统中安装了构建工具：

```bash
sudo apt update
sudo apt install -y build-essential cmake git
```

------

### **2. 下载 `gflags` 源码**

从 `gflags` 官方仓库克隆源码：

```bash
git clone https://github.com/gflags/gflags.git
cd gflags
```

------

### **3. 创建和进入构建目录**

创建独立的构建目录以保持源码整洁：

```bash
mkdir build && cd build
```

------

### **4. 配置编译选项**

运行 `cmake` 命令配置编译选项：

```bash
cmake .. -DCMAKE_CXX_FLAGS="-fPIC" -DCMAKE_POSITION_INDEPENDENT_CODE=ON -DBUILD_SHARED_LIBS=ON
```

------

### **5. 编译**

使用 `make` 命令进行编译：

```bash
make -j$(nproc)
```

- `$(nproc)` 会根据 CPU 核心数并行编译，加速构建过程。

------

### **6. 安装**

编译完成后，安装到指定路径：

```bash
sudo make install
```

------

### **7. 验证安装**

验证 `gflags` 的头文件和库文件是否安装成功：

```bash
ls /usr/local/include/gflags
ls /usr/local/lib | grep gflags
```

如果看到类似以下内容，说明安装成功：

```
/usr/local/include/gflags/gflags.h
/usr/local/lib/libgflags.so
```

------

### **8. 配置环境变量（如果必要）**

如果库文件安装到非标准路径（如 `/usr/local/lib`），需要配置环境变量：

```bash
export LIBRARY_PATH=/usr/local/lib:$LIBRARY_PATH
export LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH
```

将上述命令添加到 `~/.bashrc` 以永久生效：

```bash
echo 'export LIBRARY_PATH=/usr/local/lib:$LIBRARY_PATH' >> ~/.bashrc
echo 'export LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH' >> ~/.bashrc
source ~/.bashrc
```

------

### **9. 清理源码（可选）**

安装完成后，可以删除源码目录以节省空间：

```bash
cd ../..
rm -rf gflags
```







## 源码安装 `glog`

以下是从源码安装 `glog` 的完整步骤：

------

### **1. 安装依赖**

确保系统已安装必要的工具和库：

```bash
sudo apt update
sudo apt install -y build-essential cmake git
```

------

### **2. 下载 `glog` 源码**

从 `glog` 官方仓库克隆源码：

```bash
git clone https://github.com/google/glog.git
cd glog
```

------

### **3. 创建和进入构建目录**

创建独立的构建目录以保持源码整洁：

```bash
mkdir build && cd build
```

------

### **4. 配置编译选项**

运行 `cmake` 配置构建环境：

```bash
cmake .. -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr/local
```

#### 参数解释：

- `-DCMAKE_BUILD_TYPE=Release`: 构建优化后的发布版本。
- `-DCMAKE_INSTALL_PREFIX=/usr/local`: 安装到 `/usr/local`。

------

### **5. 编译**

使用 `make` 编译：

```bash
make -j$(nproc)
```

`$(nproc)` 会根据 CPU 核心数并行编译，加速构建过程。

------

### **6. 安装**

安装 `glog`：

```bash
sudo make install
```

------

### **7. 验证安装**

检查 `glog` 是否安装成功：

#### 检查头文件：

```bash
ls /usr/local/include/glog
```

输出类似以下内容表示成功：

```
logging.h
```

#### 检查库文件：

```bash
ls /usr/local/lib | grep glog
```

输出类似以下内容表示成功：

```
libglog.so
libglog.a
```

------

### **8. 配置环境变量（如果必要）**

如果库文件安装到了 `/usr/local/lib` 等非标准路径，添加以下环境变量：

```bash
export LIBRARY_PATH=/usr/local/lib:$LIBRARY_PATH
export LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH
```

将命令添加到 `~/.bashrc` 中以永久生效：

```bash
echo 'export LIBRARY_PATH=/usr/local/lib:$LIBRARY_PATH' >> ~/.bashrc
echo 'export LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH' >> ~/.bashrc
source ~/.bashrc
```

------

### **9. 清理源码（可选）**

安装完成后，可以删除源码以节省空间：

```bash
cd ../..
rm -rf glog
```

------

### **10. 如果需要指定 `glog` 位置**

在使用依赖 `glog` 的项目（如 `brpc`）时，手动指定 `glog` 的路径：

```bash
cmake .. -DGLOG_INCLUDE_DIR=/usr/local/include -DGLOG_LIBRARY=/usr/local/lib/libglog.so
```

------





## 源码安装 `zlib`

以下是从源码安装 `zlib` 的步骤：

------

### **1. 下载 zlib 源码**

访问 [zlib 官方网站](https://zlib.net/) 下载最新版本的源码压缩包，或者直接使用以下命令下载：

```bash
wget http://zlib.net/zlib-1.2.13.tar.gz
```

> **注意**：将 `1.2.13` 替换为你需要的具体版本号。

------

### **2. 解压源码包**

```bash
tar -xvzf zlib-1.2.13.tar.gz
cd zlib-1.2.13
```

------

### **3. 配置编译选项**

运行配置脚本，准备编译：

```bash
./configure --prefix=/usr/local/zlib
```

#### **参数说明**：

- `--prefix=/usr/local/zlib`: 指定安装路径（可以根据需要更改）。

------

### **4. 编译 zlib**

使用 `make` 编译源码：

```bash
make -j$(nproc)
```

------

### **5. 安装 zlib**

运行以下命令进行安装：

```bash
sudo make install
```

------

### **6. 配置共享库路径**

如果 `zlib` 被安装到非默认路径（例如 `/usr/local/zlib`），需要将其库路径加入系统库配置：

#### **添加到 `/etc/ld.so.conf.d/`**：

创建一个新的配置文件：

```bash
echo "/usr/local/zlib/lib" | sudo tee /etc/ld.so.conf.d/zlib.conf
```

#### **更新共享库缓存**：

```bash
sudo ldconfig
```

------

### **7. 验证安装**

通过以下命令确认 `zlib` 是否正确安装：

```bash
ldconfig -p | grep zlib
```

------

### **8. 清理安装包（可选）**

安装完成后，可以删除下载的压缩包和源码目录：

```bash
cd ..
rm -rf zlib-1.2.13 zlib-1.2.13.tar.gz
```

------





## 源码安装 OpenSSL 

以下是从源码安装 OpenSSL 的详细步骤：

------

### **1. 下载 OpenSSL 源码**

访问 [OpenSSL 官方网站](https://www.openssl.org/source/) 下载最新稳定版本的源码，或者使用以下命令下载：

```bash
wget https://www.openssl.org/source/openssl-1.1.1u.tar.gz
```

> **注意**：将 `1.1.1u` 替换为你需要的具体版本号。

------

### **2. 解压源码包**

```bash
tar -xvzf openssl-1.1.1u.tar.gz
cd openssl-1.1.1u
```

------

### **3. 配置编译选项**

运行配置脚本，准备编译：

```bash
./config --prefix=/usr/local/openssl --openssldir=/usr/local/openssl shared zlib
```

#### **参数说明**：

- `--prefix=/usr/local/openssl`: 指定安装路径（可以根据需要更改）。
- `--openssldir=/usr/local/openssl`: 指定配置文件路径。
- `shared`: 编译共享库（`.so` 文件）。
- `zlib`: 启用 `zlib` 压缩支持（需要系统中已安装 `zlib`）。

------

### **4. 编译 OpenSSL**

运行以下命令进行编译：

```bash
make -j$(nproc)
```

------

### **5. 安装 OpenSSL**

使用以下命令安装：

```bash
sudo make install
```

------

### **6. 配置共享库路径**

如果 OpenSSL 被安装到非默认路径（如 `/usr/local/openssl`），需要将其库路径加入系统库配置：

#### **添加到 `/etc/ld.so.conf.d/`**：

创建一个新的配置文件：

```bash
echo "/usr/local/openssl/lib" | sudo tee /etc/ld.so.conf.d/openssl.conf
```

#### **更新共享库缓存**：

```bash
sudo ldconfig
```

------

### **7. 替换系统默认的 OpenSSL（可选）**

如果需要使用新版本替换系统默认的 OpenSSL，可以更新路径：

#### **备份旧版本的 OpenSSL 可执行文件**：

```bash
sudo mv /usr/bin/openssl /usr/bin/openssl.bak
```

#### **创建新版本的软链接**：

```bash
sudo ln -s /usr/local/openssl/bin/openssl /usr/bin/openssl
```

------

### **8. 验证安装**

检查 OpenSSL 是否安装成功以及版本：

```bash
openssl version -a
```

------

### **9. 清理安装包（可选）**

完成安装后，可以删除下载的压缩包和源码目录：

```bash
cd ..
rm -rf openssl-1.1.1u openssl-1.1.1u.tar.gz
```

------

### **注意事项**

1. 确保安装过程中未覆盖系统关键依赖的 OpenSSL，否则可能导致系统组件异常。

2. 如果需要保留旧版本的 OpenSSL，可以同时维护多个版本，通过环境变量动态切换：

   ```bash
   export PATH=/usr/local/openssl/bin:$PATH
   ```



## 源码安装brpc

从源码安装 bRPC 的过程包括获取源码、安装依赖项、构建和安装。以下是详细步骤：

------

### 1. **获取 bRPC 源码**

bRPC 的源码托管在 GitHub 上。你可以通过以下命令克隆代码仓库：

```bash
git clone https://github.com/apache/brpc.git
cd brpc
```

------

### 2. **安装依赖项**

bRPC 需要以下依赖项：

- **Protobuf**: 用于序列化数据结构。
- **gflags**: 用于命令行参数解析。
- **glog**: 用于日志记录。
- **zlib**: 用于压缩支持。
- **OpenSSL**（可选）: 用于加密支持。

以上依赖，都已经在上述步骤介绍过了，不在赘述。

#### 安装依赖项（以 Ubuntu 为例）

```bash
sudo apt-get update
sudo apt-get install -y build-essential cmake git libprotobuf-dev protobuf-compiler libssl-dev zlib1g-dev libgflags-dev libgoogle-glog-dev
```

如果你还没有安装 Protobuf，可以选择源码编译（推荐 3.x 版本）。

------

### 3. **编译和安装 bRPC**

bRPC 支持两种构建系统：`cmake` 和 `make`。以下以两种方式分别介绍：

#### **方法一：使用 CMake**

**这也是我编译成功的方法。**

1. **生成构建文件** 确保你的 CMake 版本满足要求（建议 CMake 3.10+）。

   ```bash
   mkdir build
   cd build
   cmake .. -DGFLAGS_INCLUDE_DIR=/usr/local/include -DGFLAGS_LIBRARIES=/usr/local/lib/libgflags.so
   ```

2. **编译**

   ```bash
   make -j$(nproc)
   ```

3. **安装**

   ```bash
   sudo make install
   ```

   默认安装路径是 `/usr/local/`。你可以通过设置 `CMAKE_INSTALL_PREFIX` 更改安装路径，例如：

   ```bash
   cmake -DCMAKE_INSTALL_PREFIX=/your/custom/path ..
   ```

#### **方法二：使用 Make**

1. **配置环境** 在 bRPC 源码根目录执行：

   ```bash
   sh config_brpc.sh --headers=/usr/include --libs=/usr/lib
   ```

2. **编译**

   ```bash
   make -j$(nproc)
   ```

3. **安装** 如果没有安装错误，可以手动将生成的文件拷贝到目标路径：

   ```bash
   sudo cp -r output/include/* /usr/local/include/
   sudo cp -r output/lib/* /usr/local/lib/
   sudo ldconfig
   ```

------

### 4. **验证安装**

通过运行 bRPC 自带的单元测试验证是否安装成功：

```bash
cd test
./run_tests.sh
```

------

### 5. **常见问题排查**

#### 找不到 Protobuf

如果 CMake 或 Make 报错找不到 Protobuf，你可以通过设置环境变量指定 Protobuf 的路径：

```bash
export Protobuf_INCLUDE_DIR=/path/to/protobuf/include
export Protobuf_LIB_DIR=/path/to/protobuf/lib
```

或者在编译时传递相关路径：

```bash
cmake -DProtobuf_INCLUDE_DIR=/path/to/protobuf/include -DProtobuf_LIB_DIR=/path/to/protobuf/lib ..
```

#### 找不到依赖的库

如果编译时报缺少某些依赖库，可以尝试重新安装或检查路径是否正确。确保 `LD_LIBRARY_PATH` 中包含相关库路径，例如：

```bash
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib
```

------

### 6. **使用示例**

安装完成后，可以参考 [bRPC 官方示例](https://github.com/apache/brpc/blob/master/example/) 来测试 bRPC 的基本功能。例如运行 `echo_c++` 示例：

```bash
cd example/echo_c++/
sh run.sh
```

------



