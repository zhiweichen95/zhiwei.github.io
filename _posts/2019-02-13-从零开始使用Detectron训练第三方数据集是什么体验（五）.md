layout:     post              # 使用的布局（不需要改）
title:      从零开始使用Detectron训练第三方数据集是什么体验（五）         # 标题
subtitle:   训练
date:       2019-02-13    # 时间
author:     zhiwei        # 作者
header-img: img/post-bg-2015.jpg  #这篇文章标题背景图片
catalog: true             # 是否归档
tags:               #标签
    - Detectron
**<center><b><big>从零开始使用Detectron训练第三方数据集是什么体验（五）</b></center>**

> **本栏目分为6个部分：**
>
> - [第1部分：Detectron框架简介与安装](https://blog.csdn.net/zw__chen/article/details/86723277)
> - [第2部分（本文）：使用LabelImgPlus对图片进行标注](https://blog.csdn.net/zw__chen/article/details/87188004)
> - [第3部分：数据集处理](https://blog.csdn.net/zw__chen/article/details/87202034)
> - [第4部分：在detectron上部署数据集](https://blog.csdn.net/zw__chen/article/details/87209669)
> - 第5部分（本文）：训练
> - [第6部分：测试](https://blog.csdn.net/zw__chen/article/details/87213064)
<big><center> GitHub：https://github.com/zhiweichen95/detectron_my

<center><b><big>第5部分：训练</big></b></center>

@[toc]
# 0 前言
&emsp;&emsp;数据集在detectron框架中部署好之后，数据集相关的工作就已经完成辣~本章节将对训练部分做详细介绍。本项目使用的是retinanet网络。

# 1 配置yaml文件
&emsp;&emsp;在`Detectron\configs\12_2017_baselines`目录下，复制文件`retinanet_R-50-FPN_1x.yaml`，放到另一个目录（这其实就是①为了备份一下②方便使用）。
&emsp;&emsp;如：本人将`retinanet_R-50-FPN_1x.yaml`复制到`Detectron\configs\my`目录下重命名为`retinanet_R-50-FPN_1x1.0.yaml`
# 2 修改yaml文件
&emsp;&emsp;对`retinanet_R-50-FPN_1x1.0.yaml`进行修改，如下图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190213220156910.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p3X19jaGVu,size_16,color_FFFFFF,t_70)
- NUM_CLASSES：类别+1 ==> 3+1=4
- WEIGHTS：可以自己根据给出的网址下载权重然后指定路径；也可以默认不改。
- TRAIN：DATASETS：之前生成的voc_2007_train.json ==>('voc_2007_train',)
- TEST：DATASETS：之前生成的voc_2007_test.json ==>('voc_2007_test',)

# 3 开始训练
`cd`到$Detectron目录下执行命令：
```
./tools/train.py --cfg configs/my/retinanet_R-50-FPN_1x1.0.yaml OUTPUT_DIR experiments/1.0/
```
-  `--cfg` ：配置文件路径
- `OUTPUT_DIR `：训练的输出路径

接下来就是自行训练过程了~


<hr>
第五部分到此结束。如有任何问题，欢迎留言交流学习。

<br>

关于yaml配置文件的疑问也欢迎留言交流喔~
