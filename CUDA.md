CUDA 安装与使用指南

初识CUDA以及CUDA安装全指南

初识CUDA

<a name="CUDA_intro"></a>

CUDA简介（Compute Unified Device Architecture）

- CUDA是NVIDIA推出的一种并行计算平台和编程模型，它允许开发者利用NVIDIA显卡的GPU核心进行通用计算，大幅提升计算密集型任务的处理速度，如深度学习训练、科学计算、视频编解码等。

- 与传统CPU串行计算不同，CUDA将复杂任务拆解为多个子任务，分配给GPU上的多个流处理器同时执行，从而实现并行加速。

<a name="CUDA_core"></a>

CUDA核心组件

- CUDA Toolkit：核心开发工具集，包含编译器、库文件、调试工具等，是CUDA开发和运行的基础。

- GPU驱动：连接操作系统与GPU硬件的桥梁，必须与CUDA Toolkit版本兼容才能正常工作。

- CUDA Runtime API：提供运行时调用接口，支持动态加载GPU函数，方便开发者编写灵活的并行程序。

- CUDA Libraries：内置常用的并行计算库，如CUDA BLAS（线性代数运算）、CUDA FF T（快速傅里叶变换）等，可直接调用减少重复开发。

<a name="CUDA_scenario"></a>

典型应用场景

# 深度学习模型训练（依赖CUDA加速）
python train_model.py
# 科学计算模拟（如流体力学、分子动力学）
./scientific_simulation.exe
# 视频渲染与特效处理
ffmpeg -c:v h264_cuvid -i input.mp4 output.mp4

CUDA安装全流程

<a name="install_prepare"></a>

安装前准备

1. 硬件兼容性检查

只有NVIDIA显卡支持CUDA，需先确认显卡型号是否在CUDA支持列表内。

# Windows系统：通过命令查看显卡信息
dxdiag                     # 打开DirectX诊断工具，在"显示"选项卡查看显卡型号
# Linux系统：通过命令查看显卡信息
lspci | grep -i nvidia    # 列出NVIDIA显卡型号

2. 版本匹配确认

GPU驱动版本需与目标CUDA Toolkit版本对应，可在NVIDIA官网查询版本兼容对照表，核心原则：驱动版本≥CUDA Toolkit要求的最低驱动版本。

# 查看当前GPU驱动版本
nvidia-smi                 # 终端输入该命令，显示驱动版本和支持的最高CUDA版本

<a name="windows_install"></a>

Windows系统安装步骤

1. 卸载旧版本（如有）

若之前安装过CUDA，需先通过"控制面板-程序和功能"卸载NVIDIA CUDA Toolkit及相关组件，避免版本冲突。

2. 下载安装包

访问NVIDIA CUDA Toolkit官网，选择对应Windows系统版本（如Windows 10/11、x86_64），下载exe安装包。

3. 执行安装

# 双击安装包，按以下步骤操作
1. 选择安装路径（默认路径即可，建议不要包含中文）
2. 选择安装类型（推荐"自定义安装"，取消不需要的组件如Visual Studio Integration）
3. 等待安装完成，勾选"安装NVIDIA驱动"（若驱动版本不匹配则单独更新驱动）

4. 配置环境变量

安装完成后系统会自动配置部分环境变量，手动检查并补充以下变量（key=value格式）

  - CUDA_PATH = C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\版本号

  - CUDA_PATH_Vxx_x = C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\版本号（xx_x为具体版本，如12_4）

  - 系统PATH中添加：%CUDA_PATH%\bin;%CUDA_PATH%\libnvvp;

<a name="linux_install"></a>

Linux系统安装步骤

1. 卸载旧版本（如有）

# 执行官方卸载脚本
sudo /usr/local/cuda-X.Y/bin/cuda-uninstaller  # X.Y为旧版本号
# 清理残留文件
sudo rm -rf /usr/local/cuda-X.Y

2. 安装依赖库

# Ubuntu/Debian系统
sudo apt-get install build-essential gcc g++ make
# CentOS/RHEL系统
sudo yum install gcc gcc-c++ make

3. 下载并安装CUDA Toolkit

官网选择对应Linux发行版、版本和安装方式（推荐runfile安装），执行以下命令：

# 赋予安装包执行权限
chmod +x cuda_xx.x.xx_linux.run
# 执行安装（--toolkit仅安装核心组件，不安装驱动）
sudo ./cuda_xx.x.xx_linux.run --toolkit --silent --override

4. 配置环境变量

编辑用户环境变量配置文件，永久生效环境变量

# 打开配置文件
vim ~/.bashrc
# 在文件末尾添加以下内容（版本号替换为实际安装版本）
export CUDA_PATH=/usr/local/cuda
export PATH=$CUDA_PATH/bin:$PATH
export LD_LIBRARY_PATH=$CUDA_PATH/lib64:$LD_LIBRARY_PATH
# 生效配置
source ~/.bashrc

<a name="verify_install"></a>

安装验证

安装完成后，通过以下命令验证CUDA是否正常工作

# 查看CUDA版本
nvcc -V                     # 显示CUDA编译器版本信息，代表安装成功
# 运行官方测试用例
cd /usr/local/cuda/samples/1_Utilities/deviceQuery
make
./deviceQuery                # 输出GPU设备信息，显示"Result = PASS"则验证通过

tips: 

nvcc -V 中的V为大写，小写会报错；

若运行deviceQuery提示缺少库文件，检查LD_LIBRARY_PATH环境变量是否配置正确。

常见问题与解决办法

1. nvcc -V 命令未找到

解决：环境变量配置错误，重新检查PATH路径是否包含CUDA的bin目录，执行source命令生效配置。

2. 驱动版本不兼容

解决：根据CUDA版本要求，在NVIDIA驱动官网下载对应驱动版本重新安装。

3. Linux安装时提示gcc版本过高

解决：安装兼容版本的gcc，或在安装命令中添加--override参数强制安装。

参考文献

- 【CUDA Toolkit官方安装指南】（NVIDIA官网）

- 【Windows 11系统CUDA 12.4安装详细教程】（CSDN）

- 【Linux系统CUDA安装避坑指南】（知乎）

- 【CUDA版本与显卡驱动兼容性对照表】（NVIDIA开发者社区）
