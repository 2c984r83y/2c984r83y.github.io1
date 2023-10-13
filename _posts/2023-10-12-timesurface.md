---
layout: post
title: "浅谈Time-Surface"
date:   2023-10-12
tags: [default]
comments: true
author: 2c984r83y
---
## About事件相机

事件相机的一般输出形式是事件流，事件流可以表示成如下的形式：

$ev_i=[\mathbf{x_i},t_i,p_i]^T,\quad i\in\mathbb{N},$

$ev_i$是第i个事件，其坐标为$\mathbf{x_i}=[x_i,y_i]^T$

时间戳$t_i$，极性$p_i \in \left \{  -1,1\right \} $，-1表示OFF事件，1表示ON事件

像素异步生成事件，形成时空点云，代表对象的空间分布和动态行为。
![20231013165452](https://raw.githubusercontent.com/2c984r83y/2c984r83y.github.io/main/images/20231013165452.png)

因为事件流是异步产生的，与传统的固定事件曝光的图像相比富含更时空信息，所以将事件流转换为图像帧的算法至关重要，选对了算法就能发挥事件相机的高时间分辨率优势。事件相机与传统的高速相机相比，输出的事件流中不含冗余的背景图像信息，事件流只输出亮度变化超过阈值的运动物体的信息，这是我们感兴趣的，因此提高了处理速度，降低了数据量，避免使用过多的硬件资源，更利于实时计算。

## Timesurface

Timesurface是一种
