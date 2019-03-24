layout:     post              # 使用的布局（不需要改）
title:      从零开始使用Detectron训练第三方数据集是什么体验（一）         # 标题
subtitle:   Detectron框架简介与安装
date:       2019-02-13    # 时间
author:     zhiwei        # 作者
header-img: img/post-bg-2015.jpg  #这篇文章标题背景图片
catalog: true             # 是否归档
tags:               #标签
    - Detectron
**<center><b><big>从零开始使用Detectron训练第三方数据集是什么体验（一）<big></b></center>**

> **本栏目分为6个部分：**
>
> - 第1部分（本文）：Detectron框架简介与安装
> - [第2部分：使用LabelImgPlus对图片进行标注](https://blog.csdn.net/zw__chen/article/details/87188004)
> - [第3部分：数据集处理](https://blog.csdn.net/zw__chen/article/details/87202034)
> - [第4部分：在detectron上部署数据集](https://blog.csdn.net/zw__chen/article/details/87209669)
> - [第5部分：训练](https://blog.csdn.net/zw__chen/article/details/87210932)
> - [第6部分：测试](https://blog.csdn.net/zw__chen/article/details/87213064)
<big><center> GitHub：https://github.com/zhiweichen95/detectron_my

<center><b><big>第1部分：Detectron框架简介与安装<big></b></center>

@[toc]
# 1 Detectron简介

&emsp;&emsp;Detectron是Facebook AI研究院(FAIR)于2018年初公开的目前为止业内最佳水平的目标检测平台。据介绍，该项目自 2016 年 7 月启动，构建于 Caffe2 之上，目前支持大量机器学习算法，其中包括 Mask R-CNN（何恺明的研究，ICCV 2017 最佳论文）和 Focal Loss for Dense Object Detection，（ICCV 2017 最佳学生论文）。Facebook 称，该工具包已被应用与公司内部很多团队应用于各类应用中，一旦训练完成，这些计算机视觉模型可被部署在云端或移动设备上。

&emsp;&emsp;该工具的开源对于研究目标检测的朋友来说有极大的帮助，可以帮助他们利用最先进的深度学习目标检测技术快速构建自己的模型和应用，也可在其上开展进一步的研究，无论从工程上还是从学术研究上都有较大的参考价值。由于本人目前的方向为基于弱监督的目标检测，使用的框架为Detectron，希望能与志同道合的朋友一起交流探讨。

# 2 Detectron安装
&emsp;&emsp;Detectron是基于caffe2上的，命令安装流程如下：

    git clone https://github.com/pytorch/pytorch.git
    cd pytorch
    git checkout v0.4.1
    git submodule update --init
    mkdir build
    cd build
    cmake ..
    make -j4
    sudo make install -j

```
git submodule update --init
mkdir build && cd build
cmake -DCMAKE_CXX_FLAGS="-isystem /home/chenzhiwei/Documents/pytorch/third_party/eigen -isystem /home/chenzhiwei/Documents/pytorch/third_party/cub" ..
make
cd ..
make
```
&emsp;&emsp;若在安装过程中遇到任何问题，欢迎留言交流学习。
&emsp;&emsp;各位读者亦可以通过本人另一篇博客：[记pytorch 0.4.1caffe2源码安装](https://blog.csdn.net/zw__chen/article/details/83340818)，进行问题解决。

# 3 Caffe2 简介
&emsp;&emsp;2017年4月18日，Facebook推出了Caffe2，一个兼具表现力、速度和模块性的开源深度学习框架。它沿袭了大量的Caffe设计，可解决多年来在Caffe的使用和部署之中发现的瓶颈问题。最终，Caffe2打开了算法实验和新产品的大门。通过在内部用于各种深度学习和增强现实任务，Caffe2已经在Facebook对于规模和性能的需求上得到了锻造。同时，它为移动端应用提供了令人印象深刻的新功能，例如高级相机和即时通讯功能。

&emsp;&emsp;Detectron是Facebook AI研究院(FAIR)于2018年初公开的目前为止业内最佳水平的目标检测平台。该项目自2016年7月启动，构建于Caffe2之上，不论是学术研究还是工程项目上，在目标检测方向有极大的应用。

<hr>

&emsp;&emsp;第一部分到此结束。如有任何问题，欢迎留言交流学习。
