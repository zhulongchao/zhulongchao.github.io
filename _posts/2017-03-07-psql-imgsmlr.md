---
layout: post
title:  psql-imgsmlr:图像搜索
categories: [CV]
tags: haar
---

##1.编译imgsmlr

	$ git clone https://github.com/postgrespro/imgsmlr.git
	$ cd imgsmlr
	$ make USE_PGXS=1
	$ sudo make USE_PGXS=1 install  

##2.创建表
    psql -U postgres
    ##创建插件
    create extension imgsmlr;
    
	create table image (id serial, data bytea);
    ##pg_read_binary_file  读取的文件路径必须在 $PGDATA目录下。
	insert into image(data) select pg_read_binary_file('文件路径');
    ##图片转成pattern和signature
    CREATE TABLE pat AS (
	    SELECT
	        id,
	        shuffle_pattern(pattern) AS pattern, 
	        pattern2signature(pattern) AS signature 
	    FROM (
	        SELECT 
	            id, 
	            jpeg2pattern(data) AS pattern 
	        FROM 
	            image
	    ) x 
    );
##3.查询

SELECT
    id,
    smlr
FROM
(
    SELECT
        id,
        pattern <-> (SELECT pattern FROM pat WHERE id = :id) AS smlr
    FROM pat
    WHERE id <> :id
    ORDER BY
        signature <-> (SELECT signature FROM pat WHERE id = :id)
    LIMIT 100
) x
ORDER BY x.smlr ASC 
LIMIT 10

##4.原理

缩放到64*64

像素（rgb），求均方根。

在做统计直方Normalize，频率计算

转成patten：
haar wavelet transform

基于pattern求signature，越往左上角，权重越大

基于pattern 做shuffle，对物体旋转不敏感。


求相似度：signature，比较signature
         比较pattern，越往左上角，权重越大



String.foramt 实现字符串补足


###Haar wavelet:哈尔小波变换
小波变换的基本思想是用一组小波函数或者基函数表示一个函数或者信号。
信号分析一般是为了获得时间和频率域之间的相互关系,傅立叶变换提供了有关
频率域的信息,但时间方面的局部化信息却基本丢失。与傅立叶变换不同,小波变
换通过平移母小波(mother wavelet)可获得信号的时间信息,而通过缩放小波的宽
度 (或者叫做尺度 )可获得信号的频率特性。在小波变换中,近似值是大的缩放因
子产生的系数,表示信号的低频分量。而细节值是小的缩放因子产生的系数,

首先要注意小波基的正则
性阶数。正则性是函数光滑程度的一种描述,也是函数频域能量集中的一种度量,
正则性越差,则重建图像的变化就越不光滑,视觉效果就越差。对正则性差的小波
主要采用增加滤波器长度的方法改善重构图像质量,但带来的代价是运算量大、
速度慢。Haar 小波就属于这种小波。其次要考虑待处理图像与小波基的相似性。
对同一幅图像而言,用不同的小波基进行分解所得到的数据压缩效果是不同的,小
波基的基本图像与待处理图像的结构越相似,压缩效果就越好。再次还要考虑小
波变换的边界问题。边界失真主要是正交镜像滤波器的非线性相位特性、信号自
身在边界附近的相关性以及对变换结果亚抽样所造成的,当正交镜像滤波器的相
位特性是非线性时，经过一级分解后的结果在边界处不再具有对称性,这必然导
致重建信号在边界处产生误差,并影响到下一级分解。



1.PIL的module安装，替换成Pillow

2.pycharm 的子工程依赖关系