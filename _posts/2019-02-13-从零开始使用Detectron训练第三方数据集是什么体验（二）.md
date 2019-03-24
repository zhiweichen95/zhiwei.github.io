---
layout:     post              # 使用的布局（不需要改）
title:      从零开始使用Detectron训练第三方数据集是什么体验（二）         # 标题
subtitle:   使用LabelImgPlus对图片进行标注
date:       2019-02-13    # 时间
author:     zhiwei        # 作者
header-img: img/post-bg-2015.jpg  #这篇文章标题背景图片
catalog: true             # 是否归档
tags:               #标签
    - Detectron
---

> **本栏目分为6个部分：**
>
> - [第1部分：Detectron框架简介与安装](http://zhiweichen.top/2019/02/13/%E4%BB%8E%E9%9B%B6%E5%BC%80%E5%A7%8B%E4%BD%BF%E7%94%A8Detectron%E8%AE%AD%E7%BB%83%E7%AC%AC%E4%B8%89%E6%96%B9%E6%95%B0%E6%8D%AE%E9%9B%86%E6%98%AF%E4%BB%80%E4%B9%88%E4%BD%93%E9%AA%8C-%E4%B8%80/)
> - 第2部分（本文）：使用LabelImgPlus对图片进行标注
> - [第3部分：数据集处理](http://zhiweichen.top/2019/02/13/%E4%BB%8E%E9%9B%B6%E5%BC%80%E5%A7%8B%E4%BD%BF%E7%94%A8Detectron%E8%AE%AD%E7%BB%83%E7%AC%AC%E4%B8%89%E6%96%B9%E6%95%B0%E6%8D%AE%E9%9B%86%E6%98%AF%E4%BB%80%E4%B9%88%E4%BD%93%E9%AA%8C-%E4%B8%89/)
> - [第4部分：在detectron上部署数据集](http://zhiweichen.top/2019/02/13/%E4%BB%8E%E9%9B%B6%E5%BC%80%E5%A7%8B%E4%BD%BF%E7%94%A8Detectron%E8%AE%AD%E7%BB%83%E7%AC%AC%E4%B8%89%E6%96%B9%E6%95%B0%E6%8D%AE%E9%9B%86%E6%98%AF%E4%BB%80%E4%B9%88%E4%BD%93%E9%AA%8C-%E5%9B%9B/)
> - [第5部分：训练](http://zhiweichen.top/2019/02/13/%E4%BB%8E%E9%9B%B6%E5%BC%80%E5%A7%8B%E4%BD%BF%E7%94%A8Detectron%E8%AE%AD%E7%BB%83%E7%AC%AC%E4%B8%89%E6%96%B9%E6%95%B0%E6%8D%AE%E9%9B%86%E6%98%AF%E4%BB%80%E4%B9%88%E4%BD%93%E9%AA%8C-%E4%BA%94/)
> - [第6部分：测试](http://zhiweichen.top/2019/02/13/%E4%BB%8E%E9%9B%B6%E5%BC%80%E5%A7%8B%E4%BD%BF%E7%94%A8Detectron%E8%AE%AD%E7%BB%83%E7%AC%AC%E4%B8%89%E6%96%B9%E6%95%B0%E6%8D%AE%E9%9B%86%E6%98%AF%E4%BB%80%E4%B9%88%E4%BD%93%E9%AA%8C-%E5%85%AD/)
<big><center> GitHub：https://github.com/zhiweichen95/detectron_my

<center><b><big>第2部分：使用LabelImgPlus对图片进行标注


# 0 前言
&emsp;&emsp;对于自行采集的图片做目标检测任务，都需要对每张图片进行人工标注，使用软件工具能够大大节约时间并且容易的把图片中需要检测的目标整理下来。本章节对LabelImgPlus标注工具从安装到使用进行详细介绍。
## 0.1 数据集简介
&emsp;&emsp;用手机拍摄150张的“人骑自行车”和150张的“人骑电动自行车照片”，利用目标检测技术，检测出“人”，“自行车”, “电动自行车”.
# 1 LabelImgPlus 简介
&emsp;&emsp;LabelImg is a graphical image annotation tool. It is written in Python and uses Qt for its graphical interface. The annotation file will be saved as an XML file. The annotation format is PASCAL VOC format, and the format is the same as ImageNet.
# 2 LabelImgPlus 安装
&emsp;&emsp;本人使用的是mac，LabelImgPlus 也有mac的安装版本，但是用起来鼠标会存在延迟，所以安装了Ubuntu系统（虚拟机）进行标注，使用win系统的读者也是可以安装一个Ubuntu系统来进行标注。这个软件在win系统下安装也是比较麻烦的，本人建议安装Ubuntu虚拟机安装LabelImgPlus 。
&emsp;&emsp;本小节也是详述LabelImgPlus 在Ubuntu系统的安装步骤。如各位读者使用mac或者win系统安装遇到问题也欢迎留言交流。

1 Download project source


     $ git clone https://github.com/lzx1413/labelImgPlus

2 Check support and install dependent libraries

Ubuntu (using python2.7)
```
$ sudo apt-get install pyqt4-dev-tools
$ pip install lxml
$ pip install qdarkstyle
$ pip install numpy
$ pip install Pillow
$ pip install opencv-python
```
注：pip 安装时提示没有安装权限的时候，执行sudo pip install ×××× 指令

&emsp;&emsp;到此，LabelImgPlus 安装完成。
# 3 LabelImgPlus 使用

```
$ cd labelImgPlus
$ ./labelImg.py
```
主界面：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190213170533539.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p3X19jaGVu,size_16,color_FFFFFF,t_70,center)
- 在`PATH/labelImgPlus/data/`文件夹中，找到`predefined_classes.txt`，本项目需要检测`people, electric bicycle, bicycle`三个类别，所以需改成如下格式：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190213171737513.png)
- File-->setting 模式选择
选择DET模式（DET Mode）
- 首先File-->change default save path， 选择默认的保存路径。
- 开始标注
在主界面中选择：`Open Dir`，打开存放图片的文件夹。即可把图片载入。
点击`Create RectBox` 开始标注，框好物体后显示物体列表，直接选择列表中对应的类别即可。
保存后，①点击Next Image进行下一张图片的标注；②在默认的保存路径中会有相应的xml文件。
*‘循环’ ......*
这个标注过程是漫长的，请耐心标注吧~


# 4 数据集标注的一些坑
&emsp;&emsp;本人标注完成后，对数据集额外处理了超久，如：文件名修改、文件名修改了自然xml文件也要对应修改等等。加上使用detectron框架数据要放在一个文件夹内，不同种类分别命名会比较好。看到这里，也许各位读者还是不明白为啥要这么做，那您先跟着做吧~看到后面的训练部分，自然会明白了！现就一些文件命名坑阐述如下：
&emsp;&emsp;本项目需要检测`people, electric bicycle, bicycle`三个类别，人骑自行车150张图片，人骑电动自行车150张图片。

- 人骑自行车150张图片的命名为：

```
bicycle001.jpg
bicycle002.jpg
bicycle003.jpg
......
bicycle150.jpg
```
- 人骑电动自行车150张图片的命名为：

```
electric_bicycle001.jpg
electric_bicycle002.jpg
electric_bicycle003.jpg
......
electric_bicycle150.jpg
```
# 5 总结
&emsp;&emsp;全部标注完成后，总文件数为`150*2+150*2=600`个。分别为：
- 人骑自行车150张图片
```
bicycle001.jpg
bicycle002.jpg
bicycle003.jpg
......
bicycle150.jpg
```
- 人骑自行车150张标注xml文件

```
bicycle001.xml
bicycle002.xml
......
bicycle150.xml
```

- 人骑电动自行车150张图片
```
electric_bicycle001.jpg
electric_bicycle002.jpg
electric_bicycle003.jpg
......
electric_bicycle150.jpg
```
- 人骑电动自行车150张标注xml文件
```
electric_bicycle001.xml
electric_bicycle001.xml
......
electric_bicycle150.xml
```

<hr>
 第二部分到此结束。如有任何问题，欢迎留言交流学习。
