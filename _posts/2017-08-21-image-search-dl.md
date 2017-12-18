---
layout: post
title: 图像搜索：CNN+FAISS
categories: [DeepLearning]
tags: DeepLearning
---

## GRPC

生成java 文件
protoc --plugin=/usr/local/protoc/bin/protoc-gen-grpc-java --grpc-java_out=java --java_out=java *.proto



## inception_v4（相似类别搜索）

inception结构:

inception的出现是为了解决过拟合和计算量增加的问题。

GLM：Generalized Liner Model

MLP：Multi-Layered Perceptrons
 
卷积核分组：padding（same,shrink）/patch size/stride

卷积核也叫过滤器或者特征检测器

我们将这个大小是3x3的过滤器中的每个元素（红色小字）与图像中对应位置的值相乘，然后对它们求和，得到右边粉红色特征图矩阵的第一个元素值
![](http://upload-images.jianshu.io/upload_images/2422746-9ded7092b6d00761.gif?imageMogr2/auto-orient/strip)

通过边框四周填充0的方式，获取到same size的卷积输出

池化层：max pooling/average pooling

最常见的方式是对过滤器的结果取最大值，如下是一个2x2的最大池化窗口，取出4个中最大的
![](http://upload-images.jianshu.io/upload_images/2422746-14f6bb7dfe029658.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


激励函数：
参考:http://www.jianshu.com/p/22d9720dbf1a

利用激励函数，引入了非线性因素，使得神经网络可以逼近任意一个非线性函数
![](http://upload-images.jianshu.io/upload_images/1667471-6d3b43bce94b33de.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

sigmoid,不适用与深层网络，反向传播时，容易出现梯度消失
tanh:
ReLU:SGD（stochastic gradient descent）收敛快，但是容易die
Softmax：用于多分类神经网络输出

过拟合(overfitting)处理:
参考:http://www.jianshu.com/p/33f33714f08c
过拟合的原因一般是参数过多，对已知模型预测完美，位置模型预测很差

L正则化处理，在代价函数后面添加参数向量L1或L2范数，参考(http://www.jianshu.com/p/a47c46153326)

dropout过拟合处理：删除一些隐藏层神经元。
![](http://upload-images.jianshu.io/upload_images/4155986-ba4ba0141c70b6e8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


PCA降维


stem网络结构
![](https://static.oschina.net/uploads/space/2017/0506/114201_wPnE_2360298.png)

###tensorflow 模型训练

单台机器训练

单台机器多GPU训练 

多台机器训练

基于一个初始化的model 进行 Fine-tuning 

### Android使用该模型




##局部特征（相同目标搜索）

###bow (bag of words)

sift

sift descriptor

kmeans：
1.随机选取k个向量作为类中心。
2.计算其他向量到这k个向量的距离，近的则属于一类
3.在计算完的k个堆中，重新计算每堆的中心点(算术平均)。
4.比较新的中心点与老的中心点距离，不符合预期，则重新迭代2.3步
5.一直到新老中心的距离符合预期
K-Means会不会陷入一直选质心的过程？能够证明这个函数是可以最终收敛的函数
每个向量单位一致

离群值剔除。

kd tree：二叉树，选择均方差(体现数据的波动性大小)最大的维度开始拆分，选定中值所在的点作为根节点
说明：http://blog.csdn.net/luoshixian099/article/details/47606159

TF-IDF：
TF(Term Frequency) 指的是一个给定的词语在文档中出现的次数，如TF=0.03(3/100)标识在包括100个词语的某文档中，词语A出现了3次。

IDF(Inverse Document Frequency),表明词语的区分度，如果一个词汇在许多文档中都出现了，证明该词汇区分度不够，则权重应该较低，idf=13.238(log(10,000/1,000))表示在1万个文档中，A词汇出现了1000次，最终tf*idf 即表示该词汇权重。


L2 normalization


计算样本图片的BagOfFeature




###Fisher Vector
参考：http://blog.csdn.net/ikerpeng/article/details/41644197
1.设定混合高斯模型的个数k
2.利用样本图片训练混合高斯模型的，求解高斯模型中的参数。
3.待编码图片，提取特征
4.利用训练出来的gmm（Gaussian Mixture Model）模型，求出fisher vector(2*d+1)*k-1

视频降维编码：
http://blog.csdn.net/wzmsltw/article/details/52050112


###VLAD（vector of locally aggregated descriptors）
参考：https://ameyajoshi005.wordpress.com/2014/03/29/vlad-an-extension-of-bag-of-words/
提取特征(d维)
k-means聚类 
计算样本 与类中心的残差，得到 k个d维向量
将k个d维拉成1维k*d长度向量
对k*d的向量进行l2 正则化





##搜索

Bow的搜索：
先算待查文档与样本文档的相关系数，超过一定的值，才需计算相似性。

构建词汇，文档的权重倒排表

批量计算 查询文档与样本文档 词汇的权重距离




搜索：
ANN(Approximate Neareast Neighbor)
暴力全遍历
划分子空间。

KD-Tree

Hash: 
![](http://ose5hybez.bkt.clouddn.com/2017/0408/lsh_ex_zps0lryoykz.PNG)

汉明距离：hash编码，每个位bit不相同的个数，即汉明距离

多表Hash:
K:桶的数量
L:hash表的个数

LSH（Local Sensitive Hashing）

Multiprobe LSH
在选中的桶附近(汉明距离)选择T个桶进行遍历。multiprobe，可以减少L的数量。

K,L,T值的设置是一个trade-off

矢量量化(vector quantization)

PQ乘积量化：
将全样本的距离计算，转化为查询样本到子空间类中心的距离计算，计算次数(K(类中心数量)*M(维度拆分的段数))，称为距离池，每个样本到查询样本的距离为各子段的距离累加(从距离池中取)，将结果排序，距离最近即可，

获得相似结果。

获得精确结果：

向量间的余弦相似度：余弦相似度，利用向量之间的夹角的余弦值标识两个向量的相似性。
![](https://wikimedia.org/api/rest_v1/media/math/render/svg/a9f8c962f73f83456742caa95c89970a18a97f2e)
点积和向量长度决定了相似度
![](https://wikimedia.org/api/rest_v1/media/math/render/svg/2a8c50526e2cc7aa837477be87eff1ea703f9dec)
其中 Ai和Bi代表A和B的各分量

针对topk再做一次 brute-force search排序。

倒排乘积量化(IVFPQ)

粗量化和积量化，配合使用，通过粗量化(做一次比较粗的聚类)找到感兴趣的子空间，在子空间空间内部做积量化计算和排序，找到距离最近的topK，基于topK做一次 brute-force search精排

精排：欧氏距离

%维度之间的距离计算


##相似性度量
欧氏距离
![](http://upload-images.jianshu.io/upload_images/2203162-19350755c1ea0a4e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 
曼哈顿距离
![]( http://upload-images.jianshu.io/upload_images/2203162-3032605843e23672.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 
切比雪夫距离
 ![](http://upload-images.jianshu.io/upload_images/2203162-5f51c8c147f738e8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 
 闵可夫斯基距离
 ![](http://upload-images.jianshu.io/upload_images/2203162-53304487e9b28516.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 
 标准化欧氏距离：
 标准差(均方差)的计算：
  ![](https://gss2.bdstatic.com/9fo3dSag_xI4khGkpoWK1HF6hhy/baike/s%3D150/sign=49a3c31e8f1001e94a3c100a880f7b06/d058ccbf6c81800af3b703f9b33533fa838b47f3.jpg)
  分量标准化:
   ![]( http://upload-images.jianshu.io/upload_images/2203162-577c489940d9a861.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
   m为均值，s为标准差
   
   推导得到
    ![]( http://upload-images.jianshu.io/upload_images/2203162-d4a912c6196d456e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
    
   马氏距离
   
   ![](http://upload-images.jianshu.io/upload_images/2203162-ddcebdf8013f25fc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
   若协方差矩阵S为单位矩阵(向量之间独立同分布)。转化成欧氏距离
   若S为对角举证，则转化为标准欧氏距离
   
   
   夹角余弦相似度：
   ![](http://upload-images.jianshu.io/upload_images/2203162-d0873fde1aeea827.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
   
   
   汉明距离:
   等长字符串S1和S2，S1 转成S2，所需经历的最小变换次数，例如 1111 与 1001 之间的汉明距离为2
   
   
   杰卡德相似系数
   ![](http://upload-images.jianshu.io/upload_images/2203162-80190a27877aeefe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
   p为两个向量相同的分量个数,q向量a中不同的分量个数和r向量b中不同的分量个数
   
   相关系数与相关距离？？？
   ![](http://upload-images.jianshu.io/upload_images/2203162-914e115754a82073.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
   
   
   
##数据预处理
标准化


缩放到指定区间

0-1区间，最大最小值分别对应(0,1)

正则化：计算P范数(L1：曼哈顿距离，L2：欧氏距离)，用每个分量除以P范数即为正则化转换



降维：
说明：http://dataunion.org/13451.html

PCA(Principal Component Analysis):线性投影,保持数据信息

LDA(linear Discriminant Analysis)：线性，保持数据的区分性

LLE(locally linear embedding):非线性

Laplacian Eigenmaps：




## faiss

对于删除来说，索引构建比较耗时
可以另外构建一个删除的blacklist，索引批量重建。

尽可能过的召回结果，
删除结果中的重复内容








