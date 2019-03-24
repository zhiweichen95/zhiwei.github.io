layout:     post              # 使用的布局（不需要改）
title:      从零开始使用Detectron训练第三方数据集是什么体验（六）         # 标题
subtitle:   测试
date:       2019-02-13    # 时间
author:     zhiwei        # 作者
header-img: img/post-bg-2015.jpg  #这篇文章标题背景图片
catalog: true             # 是否归档
tags:               #标签
    - Detectron
**<center><b><big>从零开始使用Detectron训练第三方数据集是什么体验（六）</b></center>**

> **本栏目分为6个部分：**
>
> - [第1部分：Detectron框架简介与安装](https://blog.csdn.net/zw__chen/article/details/86723277)
> - [第2部分（本文）：使用LabelImgPlus对图片进行标注](https://blog.csdn.net/zw__chen/article/details/87188004)
> - [第3部分：数据集处理](https://blog.csdn.net/zw__chen/article/details/87202034)
> - [第4部分：在detectron上部署数据集](https://blog.csdn.net/zw__chen/article/details/87209669)
> -  [第5部分：训练](https://blog.csdn.net/zw__chen/article/details/87210932)
> - 第6部分（本文）：测试
<big><center> GitHub：https://github.com/zhiweichen95/detectron_my

<center><b><big>第6部分：测试</big></b></center>

@[toc]
# 0 前言
&emsp;&emsp;训练完成后，自动会进行测试输出结果。如果还需要复现测试结果，本章节内容将详述。
# 1 复现测试数据集的mAP计算

```
python ./tools/test.py --cfg configs/my/retinanet_R-50-FPN_1x1.0.yaml \
                       TEST.WEIGHTS experiments/2.0/train/voc_2007_train/retinanet/model_final.pkl
```
-  `--cfg` ：配置文件路径
- `TEST.WEIGHTS  `：进行测试模型的路径

# 2 使用一些图片进行测试并可视化
&emsp;&emsp;本项目需要测试的图片jpg存放的文件夹路径为：`$Detectron/mytest/input/`
```
python ./tools/infer_simple.py --cfg configs/my/retinanet_R-50-FPN_1x1.0.yaml \
                               --wts experiments/2.0/train/voc_2007_train/retinanet/model_final.pkl \
                               --output-dir mytest/output/ \
                               mytest/input/
```
-  `--cfg` ：配置文件路径
- `--wts  `：进行测试模型的路径
- ` --output-dir`：可视化图片的保存文件夹路径
- `mytest/input/`指的是需要测试的图片jpg存放的文件夹路径

这样就可以进行目标检测辣~

可以看看本人跑出的结果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190213223609279.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p3X19jaGVu,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190213223648895.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p3X19jaGVu,size_16,color_FFFFFF,t_70)
<hr>
本项目就到此结束啦。如有任何问题，欢迎留言交流学习。
