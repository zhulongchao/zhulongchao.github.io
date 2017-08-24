---
layout: post
title: Colmap： 学习笔记
categories: [CV]
tags: 3D-Reconstruction 
---


###1.开发环境：
yum install gcc gcc-c++ kernel-devel

yum install boost-devel  --- 跨平台c++库

yum install -y eigen3-devel ---矩阵计算库

yum install -y freeimage-devel ---支持多种格式图像处理库

yum install -y glog-devel  ---log库

yum install -y gflags-devel  ---命令行参数的库

yum install -y glew-devel ---OpenGL扩展库

yum install -y freeglut-devel --freeglut是GLUT的一个完全开源替代库

yum install - y qt5-qtbase-devel --qt5基础库

yum install -y libXmu-devel

yum install - y libXi-devel


sudo apt-get install libatlas-base-dev libsuitesparse-dev
git clone https://ceres-solver.googlesource.com/ceres-solver
cd ceres-solver
mkdir build
cd build
cmake ..
make -j
sudo make install


###特征提取
SIFT



图形格式

colorType

转成灰度图像

24位真彩色，rgb各八位

灰度处理
rgb 求均值
1.浮点算法：Gray=R*0.3+G*0.59+B*0.11
2.整数方法：Gray=(R*30+G*59+B*11)/100
3.移位方法：Gray =(R*76+G*151+B*28)>>8;
4.平均值法：Gray=（R+G+B）/3;
5.仅取绿色：Gray=G；

灰度图放大缩小

高斯模糊处理：计算不同半径下的高斯权重
![](https://imgsa.baidu.com/baike/s%3D250/sign=b09b1ea28813632711edc536a18ea056/c8ea15ce36d3d53917448dbd3887e950352ab057.jpg)

创建多层塔结构(每个塔一个缩放级别)

计算每个塔的高斯图像集和差分图像

计算极值点：比较差分图中极小或极大的点

极值点中边缘点过滤

极值点位置调整（位置偏移量和值）

计算梯度和角度（方向）

通过极值点找特征，考虑到旋转特性。

ORB：RIEF的优点在于速度，缺点也相当明显：1：不具备旋转不变性。2：对噪声敏感3：不具备尺度不变性

###特征点match：

     为特征描述子建立kdtree
     建立match

从match中拆分出inlier match的匹配项，抽取匹配点最多的那一帧

找出当前特征点的图像中二维坐标，及其三维坐标

利用相机位姿估计 计算出相机的旋转向量和平移向量（epnp）

Rodrigues

基于上一帧计算的三维点，当前帧的特征描述子，计算match




###三维点云生成

搜索当前图像当前的像素点

Find correspondences to the given observation.

Robustly estimate track using LORANSAC.


一帧帧图像计算



ceil：向上取整

pow求次方
SIFT抽取初始化：
0塔的第0层是原始图像(或你double后的图像)，往上每一层是对其下一层进行Laplacian变换（高斯卷积，其中σ值渐大，例如可以是σ, k*σ, k*k*σ…）

octave 梯度

resolution 每个梯度的level


dog 

convolution 图像卷积


 

