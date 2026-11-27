
本文档主要基于宇树官方文档等公开资料，结合实际操作流程，说明如何接入宇树机器人G1、ssh连接到机器人、安装 python 虚拟环境并进行开发。

机器人硬件配置如下：
```text
宇树G1 EDU 旗舰版硬件配置
CPU（处理器）
型号：Arm® Cortex®-A78AE 8核处理器
核心数：8核8线程
最大睿频频率：2GHz
缓存：2MB L2 + 4MB L3
内存（RAM）
容量：16GB
类型：LPDDR5
带宽：102.4 GB/s
位宽：128-bit
GPU（图形处理器）
型号：NVIDIA Ampere 架构 GPU
核心数：1024个 CUDA 核心
Tensor Core：32个
显存：16GB（与系统内存共享）
最大动态频率：918MHz
AI算力：约100 TOPS
其他关键配置
存储：2TB（英特尔® 图像处理单元 6.0）
感知系统：Intel RealSense D435i 深度相机 + 3D激光雷达
网络：WiFi6，蓝牙5.2
麦克风：4麦克风阵列
扬声器：5w扬声器
```

## 开始之前

检查所有连线是否完好，是否正常开机。机器人颈部后的拓展坞可以连鼠标和键盘，便于在机器人端进行操作。后面会分别标注是机器人端还是PC端操作。

## SSH 连接到G1（个人热点）

机器人端：
- 在拓展坞接入键鼠
- 开机，等待语音播报【零阻尼模式】。
- 键盘 win+d 回到桌面。鼠标点击右上角网络图标，选择个人热点，连接。
- 连网后鼠标或 Ctrl+Alt+T 打开终端，ip addr 查看 wlan0 的 ip 地址。（即便同一手机，多次连接也可能出现 ip 变更，如果连接不上需要及时查看）

PC 端：
连接G1可通过多种方式，推荐使用 IDE（VSCode、Cursor 等） + ssh 连接，便于开发。
- 1、打开 VSCode
- 2、Ctrl+shift+p 打开命令选择
- 3、输入 ssh，选择 “Remote-SSH：连接到主机”
- 4、选择 “配置SSH主机“
- 5、选择一个配置文件
- 6、在文件末写入G1 ip信息（后续ip变了也在这改）：
```
Host 10.248.75.213（改实际ip）
  HostName 10.248.75.213（改实际ip）
  User unitree
  Port 22
```
- 7、再从第 2 步开始执行，到第 3 步后可以看到新加入的 G1 ip，点击之后等待连接。中间会有选择系统版本（win/linux/mac）和输入用户密码的步骤，正常填写即可。

第一次连接会安装客户端，耗时稍久，后续连接会很快。

连接后，打开终端，显示如下信息：
```bash
ros:foxy(1) noetic(2) ?

```

> 这是 unitree 内置的 ros2 版本（机器人开源操作系统），G1 内核系统版本为 ubuntu 20.04，如需在终端使用 ros2，输入 1 回车即可。

若不启用 ros2，直接回车进入下一步。

## 配置 github ssh

git 已安装，正常通过终端配置 git 公钥、私钥即可。
配置、测试流程可参照 https://blog.csdn.net/2503_92145588/article/details/148669712

配置完成后即可从 github 拉取代码

## 配置虚拟环境（miniconda）

**Miniconda** 是一个轻量级的 Python 发行版，主要用于管理 Python 环境和包。它是 Anaconda 的精简版本，仅包含 **conda 包管理器、Python 及其必要的库**。

已安装在 `/home/`，若要重新安装，请参考官方文档 https://www.anaconda.com/docs/getting-started/miniconda/install/linux-install

安装后，打开新的终端，输入 conda list 验证是否安装成功。

虚拟环境具体使用方法参考官方文档 https://docs.anaconda.net.cn/working-with-conda/environments/

## python SDK 接入

unitree_sdk2 是宇树科技为新一代机器人开发的 SDK。SDK 将底层电机控制、高层运动控制等接口进行封装，并提供相关的函数接口。

官方主要提供了 C++ 和 python 两个版本，C++ 版本 SDK 请查看 https://github.com/unitreerobotics/unitree_sdk2 ，python 版本请查看 https://github.com/unitreerobotics/unitree_sdk2_python

官方 SDK 主要包含 4 个功能：
- [DDS 交互](https://github.com/unitreerobotics/unitree_sdk2_python/tree/master/example/helloworld)
- [音频播放/ttx播放](https://github.com/unitreerobotics/unitree_sdk2_python/tree/master/example/g1/audio)
- [底层运动控制](https://github.com/unitreerobotics/unitree_sdk2_python/tree/master/example/g1/low_level)
- [高层运动控制](https://github.com/unitreerobotics/unitree_sdk2_python/tree/master/example/g1/high_level)

具体使用可以参考项目 landing 文档：`/home/unitree/FRD/docs/landing.md`

## 下一步

为了更好地了解项目前后端架构、部署方式、业务逻辑，请阅读：
- 项目后端 landing 文档：`/home/unitree/FRD/docs/landing.md`
- 项目前端 landing 文档：`/home/unitree/Screen_G1/landing.md`


