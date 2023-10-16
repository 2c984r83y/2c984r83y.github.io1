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

时间戳$t_i$，极性$ p_{i}\in\{-1,1\}$，-1表示OFF事件，1表示ON事件

像素异步生成事件，形成时空点云，代表对象的空间分布和动态行为。
![20231013165452](https://raw.githubusercontent.com/2c984r83y/2c984r83y.github.io/main/images/20231013165452.png)

因为事件流是异步产生的，与传统的固定事件曝光的图像相比富含更时空信息，所以将事件流转换为图像帧的算法至关重要，选对了算法就能发挥事件相机的高时间分辨率优势。事件相机与传统的高速相机相比，输出的事件流中不含冗余的背景图像信息，事件流只输出亮度变化超过阈值的运动物体的信息，这是我们感兴趣的，因此提高了处理速度，降低了数据量，避免使用过多的硬件资源，更利于实时计算。

## Timesurface

Timesurface是一种重构事件流的方法，它将事件流转换为图像帧，使得事件流的信息可以用传统的计算机视觉算法处理.Timesurface的核心思想是将事件流的时间信息转换为空间信息，将事件流的时间戳$t_i$转换为像素的灰度，这样就可以将事件流转换为图像帧。[1]

以$\mathbf{x_i}=[x_i,y_i]^T$为中心的一个$2R+1\times2R+1$的窗口:
可以表示为:$ T_i(\mathbf{u},p)=\max_{j\leq i}\{t_j|\mathbf{x_j}=(\mathbf{x_i}+\mathbf{u}),p_j=p\} $

其中$T_i(\mathbf{u},p)$是time-context，时间上下文

$\mathbf{u}=\begin{bmatrix}u_x,u_y\end{bmatrix}^T$是窗口中的像素,$u_x\in\{-R,\ldots,R\}$ $u_y\in\{-R,\ldots,R\}$

Timesurface中的每个像素的灰度值度是时间值的编码：亮像素显示最近的活动，而暗像素接收过去更远的事件（为了清楚起见，图中仅表示与 OFF 事件相对应的时间值）
![20231013212413](https://raw.githubusercontent.com/2c984r83y/2c984r83y.github.io/main/images/20231013212413.png)
$\mathcal{S}_i(\mathbf{u},p)$是$ev_i附近的$的time-surface:
$\mathcal{S}_i(\mathbf{u},p)=e^{-(t_i-\mathcal{T}_i(\mathbf{u},p))/\tau}$
Timesurface提供了动态的时空上下文信息,指数衰减系数扩展了时间邻域中的信息
![20231013213421](https://raw.githubusercontent.com/2c984r83y/2c984r83y.github.io/main/images/20231013213421.png)
三维视角下的timesurface:
![20231013213439](https://raw.githubusercontent.com/2c984r83y/2c984r83y.github.io/main/images/20231013213439.png)

### Time-Surface Prototypes

为了构建整个平面而不是窗口的局部timesurface,这里使用incremental clustering process(增量聚类)生成timesurface.
![20231016100619](https://raw.githubusercontent.com/2c984r83y/2c984r83y.github.io/main/images/20231016100619.png)
[1] LAGORCE X, ORCHARD G, GALLUPPI F, 等. HOTS: A Hierarchy of Event-Based Time-Surfaces for Pattern Recognition[J/OL]. IEEE Transactions on Pattern Analysis and Machine Intelligence, 2017, 39(7): 1346-1359. DOI:[10.1109/TPAMI.2016.2574707](https://doi.org/10.1109/TPAMI.2016.2574707).
