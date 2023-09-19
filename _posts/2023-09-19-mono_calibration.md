---
layout: post
title: "Prophesee Mono Calibration"
date:   2023-09-12
tags: [default]
comments: true
author: 2c984r83y
---

## Intrinsics Calibration

***

**建立相机成像几何模型并矫正透镜畸变**。

使用基于事件的2D角点检测对相机进行标定。

- 首先对闪烁棋牌格进行检测：

代码路径如下所示：

> <install-prefix>/share/metavision/sdk/calibration/apps/metavision_mono_calibration_recording

- 使用检测数据进行相机内参计算：

代码路径如下所示：

> <install-prefix>/share/metavision/sdk/calibration/apps/metavision_mono_calibration



## 闪烁棋盘格与标定界面

![intrinsics_recording_visu](https://raw.githubusercontent.com/2c984r83y/2c984r83y.github.io/main/images/intrinsics_recording_visu.png)

## 代码流水线结构

![20230919095603](https://raw.githubusercontent.com/2c984r83y/2c984r83y.github.io/main/images/20230919095603.png)

### Trail Stage

使用`Metavision::TrailFilterAlgorithmT`

作用是降噪、减少事件数量，Filter的时间窗口很宽，所以事件要么是极性改变要么是新的事件，而不是同一事件多次输出。

### Pattern Detector Stage

`Metavision::CalibrationDetectionResult`

对事件进行关键点检测，同时过滤掉边缘不准确的关键点。

Pattern Detector有两种：

- `Metavision::BlinkingFrameGeneratorAlgorithm`

- `Metavision::BlinkingDotsGridDetectorAlgorithm`

### Logger Stage

收集检测结果，记录为json文件。

### Pattern Frame Generator Stage

`Metavision::CalibrationDetectionFrameGenerator`

生成包含pattern detection的图像帧。

### Frame Generation Stage

`Metavision::PeriodicFrameGenerationAlgorithm`

根据事件生成图像帧并发送到下一stage

### Frame Composition Stage

`Metavision::FrameCompositionStage`

并排显示到目前为止生成的两个帧：由帧生成器阶段生成的事件帧和由模式帧生成器阶段产生的检测帧。

### Display Stage

![20230919095631](https://raw.githubusercontent.com/2c984r83y/2c984r83y.github.io/main/images/20230919095631.png)

## 计算内参

`metavision_mono_calibration`使用`metavision_mono_calibration_recording`的2Dpattern detector生成相机内参。

在 non-fronto parallel distorted输入图像的情况下，准确估计定义校准图案的几何形状的关键点可能会出错。

该应用程序允许使用额外的细化步骤，该步骤使用第一次相机标定的数据估计来将输入图像重投影规范的前向平行图像，然后用该图像于精确定位关键点并重新估计相机参数。

## 输出结果

对于针孔相机模型而言：

distortion vector: [k1,k2,p1,p2,k3] ,

radial coefficients: k1, k2, k3 

tangential coefficients:p1, p2 

示例:

```text
$metavision_mono_calibration -i /tmp/mono_calibration
Tool showing how to use Metavision Calibration SDK to calibrate the intrinsic parameters of the camera.
Press 'q' or Escape key to leave the program.
Starting Calibration...
Calibration done.
Kept 50/52 views.
RMS reprojection error: 0.725819 pix
Camera matrix
[1711.009729400656, 0, 644.1701299941008;
 0, 1711.009729400656, 340.831295617325;
 0, 0, 1]
Distortion coefficients
[-0.08066012661438592, -0.03151075036817833, -0.00027394542171298, 0.001511891330106499, 0]
Pattern 1: 0.998101 pix
Pattern 2: 0.695803 pix
Pattern 3: 0.634835 pix

Pattern 51: 0.771156 pix
Pattern 52: 0.530244 pix
Intrinsics, extrinsics and per view RMS reprojection errors have been saved in /tmp/mono_calibration/intrinsics.json

```

## Citiation:

[Intrinsics Calibration — Metavision SDK Docs 4.3.0 documentation](https://docs.prophesee.ai/stable/samples/modules/calibration/intrinsics.html?highlight=calibrate)

