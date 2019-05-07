---
layout:     post              # 使用的布局（不需要改）
title:      CornerNet论文解读          # 标题
subtitle:   CornerNet：Detecting Objects as Paired Keypoints  #副标题
date:       2019-05-07     # 时间
author:     zhiwei        # 作者
header-img: img/post-bg-2015.jpg  #这篇文章标题背景图片
catalog: true             # 是否归档
tags:               #标签
    - 计算机视觉论文
---

论文：<https://arxiv.org/abs/1808.01244>

&emsp;&emsp;先简单回忆一下anchor box。利用anchor box进行bbox检测时，我们一般是对大小为W*H的网格进行操作的。网格中的每个位置作为多个anchor box（比如faster rcnn中每个点有9个anchor box）的中心点，然后预测每个anchor box的中心点x，y坐标偏移量和宽高缩放尺度。这样就得到了最后的bbox。

&emsp;&emsp;作者认为anchor box有两个问题：
1、anchor box的数量实在太多了，比如DSSD有40K个，RetinaNet有100K个。在那么多的anchor box里面，正负样本的比例严重失衡。并且使得训练速度很慢。（然而由于cornerNet的基础网络太大，训练起来也是很慢）
2、anchor box 的超参数太多了，比如要选多少个box啊，他们的size是多少啊，以及宽高比怎么设置啊。当与多尺度检测结合起来，这些超参数的设置就更复杂了。

CornerNet的方法为啥比基于anchor box的方法更牛逼？作者认为有两个原因：
第一个、预测anchor box的中心点坐标要考虑四个方向的因素，左、右、上和下。而cornerNet在定位边角点时只要考虑两个方向的因素，比如要定位左上边角点，我们只需要观察物体的最上边点边界和最左边的边界。
第二个、用边界点来表示bbox，会更高效：因为只使用$O(wh)$量级的边角点，就可以表示$O(w^2h^2) $量级的anchor box。



这个图展示了整个框架，但是看不懂 😂

![Xnip2019-05-07_19-48-15](http://wx4.sinaimg.cn/mw690/007ccxpDgy1g2szvm93s6j30uk0g7gtt.jpg)

那看下一个图：

![Xnip2019-05-07_19-51-43](http://wx4.sinaimg.cn/mw690/007ccxpDgy1g2szx62ovzj311z09m0yi.jpg)

输入图片经过HG基础网络后，得到一个特征图，然后将该特征图作为两个Prediction模块的输入，分别是Top-left corners和Bottom-right corners。在每个预测模块里面，先经过Corner Pooling，然后输出Heatmaps,Embeddings,Offsets分支。我们先不管Corner Pooling了，先认为是一个普通的Pooling，只是这个Pooling不改变特征图尺寸。

Heatmaps的大小是128*128，通道为80（coco数据，80类），所以Heatmaps是一个大小为128*128*80的张量。以Top-left corners模块中的Heatmaps为例，其每个通道代表coco数据中的80个类别，每个通道的128*128网格中的每个点的输出表示该点是一个左上角点的概率。并且该通道预测出来的所有左上角点对应的所有bbox的物体类别都一样。举个例子，假设在通道编号为10（假设物体类别是dog）的128*128热图里面，预测出了3个左上角点，也就是有3个bbox，那么这3个bbox的物体类别都是dog。

Offsets大小为128*128，通道为2（分别为边角点x，y的偏移量）。由于用热图来表示某一点的xy坐标，会损失精度。因此cornerNet还对每个边角点预测其真实位置的偏移量。举个例子，假设通过热图预测出某一左上角点的坐标是（50，50），该坐标的对应尺度是热图的128*128，而真实的坐标从网络的输入尺度511*511，映射到128*128时是（50.2,50.6）这样就损失了精度了。现在cornerNet为（50，50）预测了偏移量为（0.3，0.5），那么可以计算出网络最终的预测坐标为（50.3，50.5）。尽管比真正的坐标（50.2，50.6）差了一点，但比（50，50）还是精确了许多。

Embedding大小为128*128，通道为1。这个Embedding的作用是什么呢？现在假设两个预测模块已经分别预测出了100个左上角点，和100个右下角点。那么有个问题，怎么知道哪一个左上角点和哪一个右上角点是属于同一个bbox呢？解决方法就是使用这个Embedding。Top-left预测模块和Bottom-right预测模块都有一个128*128的Embedding，简单来说，这两个Embedding中的每个值相当于一个标签。假设在Top-left预测模块里的Embedding的（50，50）位置的值是10（这个值是多少无所谓），那么其表示Top-left位置为（50，50）的左上角点的标签是10。如果，在Bottom-right预测模块里的Embedding的（100，100）位置的值也是10。那么就认为左上角坐标（50，50）和右下角坐标（100，100）属于同一个bbox。在实际应用中，属于同一个bbox的左上角和右下角的embedding值一般不会完全一样（到后面的Emdedding学习就可以知道为啥了），所以论文在判断左上角点和右上角点是否属于同一bbox的时是通过判断两个点所对应的embedding值的距离。

那么，整个检测过程大致如下：cornerNet分别输出左上角预测模块和右下角预测模块的Heatmaps、Embeddings和Offsets后。首先利用各自的Heatmaps计算出xy坐标，然后利用各自的Offsets修正xy坐标。接着利用各自的Embeddings信息收集同一bbox的左上角点和右下角点。

## 边角点检测
cornerNet在预测左上角点和右上角点时，首先会分别生成大小为W*H*C的热图。其中C表示通道，其值等于物体检测的物体类别数，比如coco的80个类别。

正常情况下，可以不使用热图，而是每一通道中的W*H矩阵中每个位置要么是0要么是1，1表示该位置是一个边角点。但是这样一来，由于bbox比较少，也就是在W*H的矩阵中值为1的位置会很少，而0会很多。即正位置很少，负位置很多。在训练过程中，不会对所有的负位置采用同样的惩罚权重，而是在正位置附近的负位置减少惩罚权重。相当于，在预测的时候将正位置附近的负位置认为是正位置也是可接受的。那么这个附近点怎么确定呢？这时候热图就起作用了。热图的生成利用的是2D高斯函数，$\exp \left(-\frac{x^{2}+y^{2}}{2 \sigma^{2}}\right)$（这是一个没有归一化的高斯函数），x，y是正位置的领域点的坐标，正位置的领域点一般是取某一半径范围内的点。这个半径可以根据物体大小来确定，具体可以参考作者提供的代码。$\sigma$是半径值的三分之一。

作者使用了一个变种的focal loss作为损失函数：

![image](http://wx3.sinaimg.cn/mw690/007ccxpDgy1g2szzmd5a4j30gp02hwep.jpg)

其中pcij 表示类别c并且位置为（i，j）是边角点的概率。ycij 是真实标签，如果某一真实bbox的边角点的位置是（i,j）并且物体类别是c那么ycij=1 。N是所有的真实bbox个数。α,β 是超参数。上面的公式中，当ycij !=1 时，也就是负位置时，前面加多了 1−ycij ，这个就是用来减少正位置附近的位置的惩罚权重。

在前面的概览中，提到了offsets。offsets就是为了避免将真实边角点坐标用热图表示而引起精度损失而引入的。假设第k个真实的边角点的位置为xk, yk ，可以计算其偏移量为：![image](http://ws3.sinaimg.cn/mw690/007ccxpDgy1g2t0211j2cj30bh02daa1.jpg)

其中n表示网络输入尺寸与网络输出尺寸的比值，或者说下采样因子。在实际使用中，所有类别的边角点都共用一个offsets。在训练的时候采用smooth L1损失函数：
![image](http://ws2.sinaimg.cn/mw690/007ccxpDgy1g2t037lipgj30cb02i0ss.jpg)

## 边角点聚集
由于在一张图片中有可能有多个物体，这时候就会检测出来多个左上角点和右下角点。那么就需要确定某一对左下角点和右下角点对是否属于同一个bbox。这位问题在概览中的embedding已经有了一定的描述。这边主要看是如何学习embedding的。根据两个embedding的功能，希望网络输出的两个embedding中，属于同一bbox的左上角点和右下角点的embdding值应该是要很相近的。为此，可以使用以下损失函数：
![image](http://wx4.sinaimg.cn/mw690/007ccxpDgy1g2t03vnpy0j30gs07taan.jpg)
k表示第k个bbox，etk、ebk  分别表示第k个bbox左上角点的embedding值和右下角点的embedding值。而ek 则是etk 和ebk 的均值。Lpull目的是使属于同一bbox的边角点的embedding值尽量一样。Lpush 目的是使不同的bbox的embedding值要有区分度。

## Corner Pooling
在确定一个像素是否是左上角点时，需要在水平方向上往右看，在竖直方向上往下看。在确定是否时右下角点时，则需要在水平方向上往左看，在竖直方向上往上看。因此作者提出了corner polling。具体如下图：
![image](http://ws1.sinaimg.cn/mw690/007ccxpDgy1g2t063a56bj30na0d5jtu.jpg)
上图展示的时top-left corner poolling，对于左上角的corner pooling根据一开始的分析，需要分别往下扫描和往右扫描，然后分别取最大值，最后相加。下图是一个实际的例子：
![image](http://ws1.sinaimg.cn/mw690/007ccxpDgy1g2t06nvq7oj30mt09kq43.jpg)

## 预测模块结构
预测模块的结构图如下：
![image](http://ws4.sinaimg.cn/mw690/007ccxpDgy1g2t078kfcuj30uc0bfdhu.jpg)

## 实验结果
![image](http://ws3.sinaimg.cn/mw690/007ccxpDgy1g2t07vwf6kj31260grk0g.jpg)