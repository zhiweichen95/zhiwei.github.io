---
layout:     post              # 使用的布局（不需要改）
title:      从零开始使用Detectron训练第三方数据集是什么体验（六）         # 标题
subtitle:   测试
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
> - [第4部分：在detectron上部署数据集](http://zhiweichen.top/2019/02/13/%E4%BB%8E%E9%9B%B6%E5%BC%80%E5%A7%8B%E4%BD%BF%E7%94%A8Detectron%E8%AE%AD%E7%BB%83%E7%AC%AC%E4%B8%89%E6%96%B9%E6%95%B0%E6%8D%AE%E9%9B%86%E6%98%AF%E4%BB%80%E4%B9%88%E4%BD%93%E9%AA%8C-%E5%9B%9B/)
> -  [第5部分：训练](http://zhiweichen.top/2019/02/13/%E4%BB%8E%E9%9B%B6%E5%BC%80%E5%A7%8B%E4%BD%BF%E7%94%A8Detectron%E8%AE%AD%E7%BB%83%E7%AC%AC%E4%B8%89%E6%96%B9%E6%95%B0%E6%8D%AE%E9%9B%86%E6%98%AF%E4%BB%80%E4%B9%88%E4%BD%93%E9%AA%8C-%E4%BA%94/)
> - 第6部分（本文）：测试
<big><center> GitHub：https://github.com/zhiweichen95/detectron_my

<center>第6部分：测试</center>

# 0 前言
&emsp;&emsp;训练完成后，自动会进行测试输出结果。如果还需要复现测试结果，本章节内容将详述。
# 1 复现测试数据集的mAP计算

```python
python ./tools/test.py --cfg configs/my/retinanet_R-50-FPN_1x1.0.yaml \
                       TEST.WEIGHTS experiments/2.0/train/voc_2007_train/retinanet/model_final.pkl
```
-  `--cfg` ：配置文件路径
- `TEST.WEIGHTS  `：进行测试模型的路径

# 2 使用一些图片进行测试并可视化
&emsp;&emsp;本项目需要测试的图片jpg存放的文件夹路径为：`$Detectron/mytest/input/`
```python
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
![](https://wx3.sinaimg.cn/large/007ccxpDly1g1hos827bcj30cu07679q.jpg)
![](https://ws4.sinaimg.cn/large/007ccxpDly1g1hosk1uwlj30cy0dmakg.jpg)
<hr>
本项目就到此结束啦。如有任何问题，欢迎留言交流学习。

