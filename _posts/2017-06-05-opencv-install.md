---
layout: post
title: centos下编译 opencv
categories: [Middleware]
tags: DRPC 
---
比较全的博文参考:

http://blog.csdn.net/daunxx/article/details/50506625


依赖工具

    yum install -y autoconf automake cmake freetype-devel gcc gcc-c++ git libtool make mercurial nasm pkgconfig zlib-devel
    
    
    yum install -y gtk2-devel
    
    yum install libdc1394-devel -y
    
    yum install libv4l-devel -y

安装 ffmpeg-devel

    首先安装  Nux Dextop repository
    rpm --import http://li.nux.ro/download/nux/RPM-GPG-KEY-nux.ro
    rpm -Uvh http://li.nux.ro/download/nux/dextop/el7/x86_64/nux-dextop-release-0-1.el7.nux.noarch.rpm
    yum repolist

然后再安装 yum install ffmpeg-devel -y 

    yum install vlc -y
    yum install gstreamer-plugins-base-devel -y
    ipp_icv下载：https://raw.githubusercontent.com/Itseez/opencv_3rdparty/81a676001ca8075ada498583e4166079e5744668/ippicv/ippicv_linux_20151201.tgz
    yum install libpng-devel libjpeg-turbo-devel jasper-devel openexr-devel libtiff-devel libwebp-devel -y
    
    yum install tbb-devel eigen3-devel -y



centos6版本下不需要安装jasper

cmake -D CMAKE_BUILD_TYPE=RELEASE  -D BUILD_SHARED_LIBS=OFF -D ENABLE_PRECOMPILED_HEADERS=OFF  -D CMAKE_INSTALL_PREFIX=/usr/local ..


利用cmake-gui 设置 输出 world 库，可以把opencv库输出为一个lib文件。

