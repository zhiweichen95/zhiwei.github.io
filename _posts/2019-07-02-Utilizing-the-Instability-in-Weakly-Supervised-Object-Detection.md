---
layout:     post              # 使用的布局（不需要改）
title:      CornerNet论文解读          # 标题
subtitle:   Utilizing the Instability in Weakly Supervised Object Detection  #副标题
date:       2019-07-02     # 时间
author:     zhiwei        # 作者
header-img: img/post-bg-2015.jpg  #这篇文章标题背景图片
catalog: true             # 是否归档
tags:               #标签
    - 计算机视觉论文
---

论文：<[Utilizing the Instability in Weakly Supervised Object Detection
openaccess.thecvf.com/.../Weakly%20Supervised%20Learning%20for%20R...](http://openaccess.thecvf.com/content_CVPRW_2019/papers/Weakly Supervised Learning for Real-World Computer Vision Applications/Liu_Utilizing_the_Instability_in_Weakly_Supervised_Object_Detection_CVPRW_2019_paper.pdf)>

本文主要贡献：

通过分析检测器多示例学习（MIL）的不稳定性，提出了

1. An end-to-end framework
2. An online fusion strategy

## 0 检测器多示例学习（MIL）的不稳定性

0.1 不同初始化参数的方法会很大程度上影响proposal的置信度如下图前三列，代表不同初始化参数的MIL-based Detector，可以看出不同分类器所给出的proposal不同，红色的表示negative，绿色的表示positive。

> 博主有话说：这里同一个分类器每个图的框应该是当前score最高的proposal（文中没有说）；具体如何训练的detector也没有说（网络框架？迭代次数？参数初始化？等）

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190702161222225.png)

0.2 为更好分析不稳定性，作者进行了定量分析，提出了IDR（Inconsistent Detection Rate）
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190702162704439.png)
本实验是通过对WSDDN不同参数初始化进行十次，随机选取两个检测器的结果根据以上公式进行定量分析，可视化如下图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019070216312014.png)

> 博主有话说：不是很懂这里。

0.3 通过对不同初始化参数的检测器多示例学习的结果分析，作者发现很容易陷入局部最优解，如下图
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019070216343468.png)

## 1 An end-to-end framework

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190702163607515.png)

> 博主有话说：其实就是基于WSDDN框架，把对于proposal的分类的softmax分支进行了扩充，最终还是采用对类的softmax和对proposal的softmax分支相乘，做普通的交叉熵损失。
> 其中，每个分支初始化不同，本文只说了采用另一篇文章的“Orthogonal Initialization”（正交初始化）的方法进行参数初始化，但是好像没有说明白具体怎么初始化，后文针对这个初始化和高斯初始化进行了对比，高斯初始化参数好像没有在文中详细说明，目前（2019.7.2）论文代码好像木有开源。
>
> ## 2 An online fusion strategy
>
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190702164251608.png)
> 博主有话说：简单的说，这个策略就是把每个分支检测的top-N拿出来，对这些proposal进行筛选，目前不太清楚上图第10行的 “is surrounded by”，到底是怎么surrounded的。如果是surrounded，那么去除这个proposal，最后对剩下的proposal作值域为0.1的NMS产生最终的proposal。个人感觉这里说的比较模糊，详细还是得看代码。

## 3 实验

最后就是实验部分了，在voc2007和voc2012数据集上进行了测试，都取得了好的结果。此外，作者还对分支的数量进行了实验（为3的时候最好）。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190702164807494.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019070216482766.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190702164849777.png)

<hr>
本人拙见，请各位读者指教！

