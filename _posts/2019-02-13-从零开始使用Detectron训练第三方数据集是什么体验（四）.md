---
layout:     post              # 使用的布局（不需要改）
title:      从零开始使用Detectron训练第三方数据集是什么体验（四）         # 标题
subtitle:   在detectron上部署数据集
date:       2019-02-13    # 时间
author:     zhiwei        # 作者
header-img: img/post-bg-2015.jpg  #这篇文章标题背景图片
catalog: true             # 是否归档
tags:               #标签
    - Detectron
    - Caffe2
---

> **本栏目分为6个部分：**
>
> - [第1部分：Detectron框架简介与安装](http://zhiweichen.top/2019/02/13/%E4%BB%8E%E9%9B%B6%E5%BC%80%E5%A7%8B%E4%BD%BF%E7%94%A8Detectron%E8%AE%AD%E7%BB%83%E7%AC%AC%E4%B8%89%E6%96%B9%E6%95%B0%E6%8D%AE%E9%9B%86%E6%98%AF%E4%BB%80%E4%B9%88%E4%BD%93%E9%AA%8C-%E4%B8%80/)
> - [第2部分（本文）：使用LabelImgPlus对图片进行标注](http://zhiweichen.top/2019/02/13/%E4%BB%8E%E9%9B%B6%E5%BC%80%E5%A7%8B%E4%BD%BF%E7%94%A8Detectron%E8%AE%AD%E7%BB%83%E7%AC%AC%E4%B8%89%E6%96%B9%E6%95%B0%E6%8D%AE%E9%9B%86%E6%98%AF%E4%BB%80%E4%B9%88%E4%BD%93%E9%AA%8C-%E4%BA%8C/)
> - [第3部分：数据集处理](http://zhiweichen.top/2019/02/13/%E4%BB%8E%E9%9B%B6%E5%BC%80%E5%A7%8B%E4%BD%BF%E7%94%A8Detectron%E8%AE%AD%E7%BB%83%E7%AC%AC%E4%B8%89%E6%96%B9%E6%95%B0%E6%8D%AE%E9%9B%86%E6%98%AF%E4%BB%80%E4%B9%88%E4%BD%93%E9%AA%8C-%E4%B8%89/)
> - 第4部分（本文）：在detectron上部署数据集
> - [第5部分：训练](http://zhiweichen.top/2019/02/13/%E4%BB%8E%E9%9B%B6%E5%BC%80%E5%A7%8B%E4%BD%BF%E7%94%A8Detectron%E8%AE%AD%E7%BB%83%E7%AC%AC%E4%B8%89%E6%96%B9%E6%95%B0%E6%8D%AE%E9%9B%86%E6%98%AF%E4%BB%80%E4%B9%88%E4%BD%93%E9%AA%8C-%E4%BA%94/)
> - [第6部分：测试](http://zhiweichen.top/2019/02/13/%E4%BB%8E%E9%9B%B6%E5%BC%80%E5%A7%8B%E4%BD%BF%E7%94%A8Detectron%E8%AE%AD%E7%BB%83%E7%AC%AC%E4%B8%89%E6%96%B9%E6%95%B0%E6%8D%AE%E9%9B%86%E6%98%AF%E4%BB%80%E4%B9%88%E4%BD%93%E9%AA%8C-%E5%85%AD/)
<big><center> GitHub：https://github.com/zhiweichen95/detectron_my

<center>第4部分：在detectron上部署数据集</center>

# 0 前言
&emsp;&emsp;本节将对上一节处理好的数据集部署在detectron框架中。

# 1 创建文件夹
&emsp;&emsp;按照如下的文件树创建文件夹。

![](<https://wx2.sinaimg.cn/large/007ccxpDly1g1hoh2wg1nj305q06a0sl.jpg>)



# 2 放入数据集

- annotations文件夹：
  把上一小节生成的json文件全部放入。

  ![](https://ws4.sinaimg.cn/large/007ccxpDly1g1hojz5hcej307d01q3ya.jpg)

- JPEGImages文件夹
  把所有的图片（训练集+测试集=300张全部放入），这就是为啥文件名都要取不一样的。
  ![](https://wx2.sinaimg.cn/large/007ccxpDly1g1hol97gdyj30mt0d2ta7.jpg)

- VOC2007\VOCdevkit2007\VOC2007\Annotations 文件夹
  把第二部分生成的所有的xml文件全部放入（训练集+测试集=300）
  ![](https://ws2.sinaimg.cn/large/007ccxpDly1g1homixj60j30mf0d7myc.jpg)

- VOC2007\VOCdevkit2007\VOC2007\ImageSets\Main 文件夹
  把上一节中生成的8个txt文件夹移入。
  ![](https://ws4.sinaimg.cn/large/007ccxpDly1g1hon5unb3j307g04tq2u.jpg)

- VOC2007\VOCdevkit2007\VOC2007\JPEGImages 文件夹
  把所有的图片移入。
  ![](https://ws1.sinaimg.cn/large/007ccxpDly1g1honflyfyj30l40d875n.jpg)
  特别说明：其他没有说明的文件夹都为空文件夹。
# 3 在detectron框架中部署数据集
- 下载仓库`git clone https://github.com/facebookresearch/Detectron.git`

- 将VOC2007整个文件夹移动到 `PATH/Detectron/detectron/datasets/data/`下

<hr>
 第四部分到此结束。如有任何问题，欢迎留言交流学习。

