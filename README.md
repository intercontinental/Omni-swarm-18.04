# Omni-swarm-18.04

This repository is a modified version of [Omni-swarm](https://github.com/HKUST-Aerial-Robotics/Omni-swarm), which can be tested on an NVIDIA Jetson NANO.

|              | Version |
| :----------: | :-----: |
|    Ubuntu    |  18.04  |
|     CUDA     |  10.2   |
|    OpenCV    | 3.4.20  |
| ceres-solver | 1.14.0  |
|    faiss     |  1.6.3  |

------

# Omni-swarm

A Decentralized Omnidirectional Visual-Inertial-UWB State Estimation System for Aerial Swarm

一种面向航空集群的分布式全向视觉惯性超宽带状态估计系统



## Introduction

**Omni-swarm** is a decentralized omnidirectional visual-inertial-UWB state estimation system for the aerial swarm. In order to solve the issues of observability, complicated initialization, insufficient accuracy and lack of global consistency, we introduce an omnidirectional perception system as the front-end of the **Omni-swarm**, consisting of omnidirectional sensors, which includes stereo fisheye cameras and ultra-wideband (UWB) sensors, and algorithms, which includes fisheye visual inertial odometry (VIO), multi-drone map-based localization and visual object detection. A graph-based optimization and forward propagation working as the back-end of the **Omni-swarm** to fuse the measurements from the front-end. According to the experiment result, the proposed decentralized state estimation method on the swarm system achieves centimeter-level relative state estimation accuracy while ensuring global consistency. Moreover, supported by the **Omni-swarm**, inter-drone collision avoidance can be accomplished in a whole decentralized scheme without any external device, demonstrating the potential of **Omni-swarm** to be the foundation of autonomous aerial swarm flights in different scenarios. 

**Omni-swarm** 是一个用于航空集群的分布式全向视觉惯性 UWB 状态估计系统。为了解决可观测性、初始化复杂、精度不足、缺乏全局一致性等问题，我们引入了全向感知系统作为 **Omni-swarm** 的前端，该系统由全向传感器组成，包括立体鱼眼摄像头和超宽带 （UWB） 传感器，以及算法，包括鱼眼视觉惯性里程计 （VIO）、基于多无人机地图的定位和视觉对象检测。基于图的优化和前向传播，作为 **Omni-swarm** 的后端，用于融合来自前端的测量结果。根据实验结果，所提出的在集群系统上的分散状态估计方法在保证全局一致性的同时，实现了厘米级的相对状态估计精度。此外，在 **Omni-swarm** 的支持下，无人机间避碰可以在一个完整的去中心化方案中完成，无需任何外部设备，展示了 **Omni-swarm** 在不同场景下成为自主空中集群飞行基础的潜力。



The is the code for __Omni-swarm: A Decentralized Omnidirectional Visual-Inertial-UWB State Estimation System for Aerial Swarms__. The manuscript has been accepted by IEEE Transactions on Robotics (T-RO), a preprint version is [here](https://arxiv.org/abs/2103.04131).

这是 **Omni-swarm 的代码：用于航空集群的分布式全向视觉惯性 UWB 状态估计系统**。该手稿已被 IEEE Transactions on Robotics （T-RO） 接受，预印[本版本在这里](https://arxiv.org/abs/2103.04131)。



The structure of Omni-swarm is

Omni-swarm 的结构是

![](./doc/structure.PNG)

The fused measurements of Omni-swarm:

Omni-swarm 的融合测量：

![](./doc/measurements.PNG)

The detailed backend structure of state estimation of Omni-swarm:

Omni-swarm 状态估计的详细后端结构：

![](./doc/backend.PNG)



## Usage 用法

The Omni-swarm offical support TX2 with Ubuntu 18.04. For those running on other hardware and system setup, converting the models to trt by your own is essential.

Omni-swarm 官方支持 Ubuntu 18.04 的 TX2。对于在其他硬件和系统设置上运行的用户，您必须自行将模型转换为 trt 。



[Here](https://www.dropbox.com/s/skq1vgfeawiw151/models.zip?dl=0) to download the CNN models for Omni-swarm and extract it to swarm_loop folder.

[在此处](https://www.dropbox.com/s/skq1vgfeawiw151/models.zip?dl=0)下载 Omni-swarm 的 CNN 模型并将其解压缩到swarm_loop文件夹中。



[Here](https://www.dropbox.com/sh/w5yagas06a9r14d/AACdKgMfCCg07M6jr6Ipmus1a?dl=0) to get the raw and preprocessed offical omni-directional and pinole dataset.

[在这里](https://www.dropbox.com/sh/w5yagas06a9r14d/AACdKgMfCCg07M6jr6Ipmus1a?dl=0)获取原始和预处理的 offical omni-directional 和 pinole 数据集。



[swarm_msgs](https://github.com/HKUST-Swarm/swarm_msgs) [inf_uwb_ros](https://github.com/HKUST-Swarm/inf_uwb_ros) are compulsory.

[swarm_msgs](https://github.com/HKUST-Swarm/swarm_msgs)[inf_uwb_ros](https://github.com/HKUST-Swarm/inf_uwb_ros) 是强制性的。



And [swarm_detector](https://github.com/HKUST-Swarm/swarm_detector) if you want to use detector, First, running the pinhole or fisheye version of [VINS-Fisheye](https://github.com/HKUST-Aerial-Robotics/VINS-Fisheye) (Yes, VINS-Fisheye is pinhole compatiable and is essential for Omni-swarm).

[swarm_detector](https://github.com/HKUST-Swarm/swarm_detector)如果你想使用探测，首先，运行 [VINS-Fisheye](https://github.com/HKUST-Aerial-Robotics/VINS-Fisheye) 的针孔或鱼眼版本（是的，VINS-Fisheye 是针孔兼容的，对于 Omni-swarm 来说是必不可少的）。



Start map-based localization with

开始基于地图的本地化

>roslaunch swarm_loop nodelet-sfisheye.launch

or pinhole version

或针孔版本

>roslaunch swarm_loop realsense.launch



Start visual object detector by (not compulsory)

通过以下方式启动视觉对象检测器（非强制性）

> roslaunch  swarm_detector detector.launch



Start UWB communication module with (Support NoopLoop UWB module only)

启动 UWB 通信模块（仅支持 NoopLoop UWB 模块）

>roslaunch localization_proxy uwb_comm.launch start_uwb_node:=true



If you don't have a UWB module, you may start the communication with a self id(start from 1, different on each drone).

如果你没有 UWB 模块，你可以用 self id 开始通信（从 1 开始，每架无人机都不同）

>roslaunch localization_proxy uwb_comm.launch start_uwb_node:=true enable_uwb:=false self_id:=1



Start state estimation with visualizer by

使用 Visualizer 开始状态估计

>roslaunch swarm_localization loop-5-drone.launch bag_replay:=true viz:=true enable_distance:=false cgraph_path:=/home/your-name/output/graph.dot



You may enable/disable specific measurement by adding

您可以通过添加

>enable_distance:=false or enable_detection:=false enable_loop:=true



To visualize the real-time estimation result, use __viz:=true__. Add __bag_replay:=true__ only when evaluation dataset, when evaluate pre-processed dataset, you may only launch __loop-5-drone.launch __Some analysis tools is located in [DataAnalysis](swarm_localization/DataAnalysis).

要直观显示实时估计结果，请使用 **viz：=true**。仅在评估数据集时添加 **bag_replay：=true**，在评估预处理后的数据集时，您只能启动 **loop-5-drone.launch** 一些分析工具位于中

