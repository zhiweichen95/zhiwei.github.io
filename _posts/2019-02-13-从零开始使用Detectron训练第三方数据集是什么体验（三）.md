---
layout:     post              # 使用的布局（不需要改）
title:      从零开始使用Detectron训练第三方数据集是什么体验（三）         # 标题
subtitle:   数据集处理
date:       2019-02-13    # 时间
author:     zhiwei        # 作者
header-img: img/post-bg-2015.jpg  #这篇文章标题背景图片
catalog: true             # 是否归档
tags:               #标签
    - Detectron
---
**<center><b><big>从零开始使用Detectron训练第三方数据集是什么体验（三）</b></center>**

> **本栏目分为6个部分：**
>
> - [第1部分：Detectron框架简介与安装](http://zhiweichen.top/2019/02/13/%E4%BB%8E%E9%9B%B6%E5%BC%80%E5%A7%8B%E4%BD%BF%E7%94%A8Detectron%E8%AE%AD%E7%BB%83%E7%AC%AC%E4%B8%89%E6%96%B9%E6%95%B0%E6%8D%AE%E9%9B%86%E6%98%AF%E4%BB%80%E4%B9%88%E4%BD%93%E9%AA%8C-%E4%B8%80/)
> - [第2部分（本文）：使用LabelImgPlus对图片进行标注](http://zhiweichen.top/2019/02/13/%E4%BB%8E%E9%9B%B6%E5%BC%80%E5%A7%8B%E4%BD%BF%E7%94%A8Detectron%E8%AE%AD%E7%BB%83%E7%AC%AC%E4%B8%89%E6%96%B9%E6%95%B0%E6%8D%AE%E9%9B%86%E6%98%AF%E4%BB%80%E4%B9%88%E4%BD%93%E9%AA%8C-%E4%BA%8C/)
> - 第3部分（本文）：数据集处理
> - [第4部分：在detectron上部署数据集](http://zhiweichen.top/2019/02/13/%E4%BB%8E%E9%9B%B6%E5%BC%80%E5%A7%8B%E4%BD%BF%E7%94%A8Detectron%E8%AE%AD%E7%BB%83%E7%AC%AC%E4%B8%89%E6%96%B9%E6%95%B0%E6%8D%AE%E9%9B%86%E6%98%AF%E4%BB%80%E4%B9%88%E4%BD%93%E9%AA%8C-%E5%9B%9B/)
> - [第5部分：训练](http://zhiweichen.top/2019/02/13/%E4%BB%8E%E9%9B%B6%E5%BC%80%E5%A7%8B%E4%BD%BF%E7%94%A8Detectron%E8%AE%AD%E7%BB%83%E7%AC%AC%E4%B8%89%E6%96%B9%E6%95%B0%E6%8D%AE%E9%9B%86%E6%98%AF%E4%BB%80%E4%B9%88%E4%BD%93%E9%AA%8C-%E4%BA%94/)
> - [第6部分：测试](http://zhiweichen.top/2019/02/13/%E4%BB%8E%E9%9B%B6%E5%BC%80%E5%A7%8B%E4%BD%BF%E7%94%A8Detectron%E8%AE%AD%E7%BB%83%E7%AC%AC%E4%B8%89%E6%96%B9%E6%95%B0%E6%8D%AE%E9%9B%86%E6%98%AF%E4%BB%80%E4%B9%88%E4%BD%93%E9%AA%8C-%E5%85%AD/)
<big><center> GitHub：https://github.com/zhiweichen95/detectron_my

<center><b><big>第3部分：数据集处理</big></b></center>

@[toc]
# 0 前言
&emsp;&emsp;本节将对上一节使用LabelImgPlus工具标注好的数据需要进一步处理。对于Detectron框架来说，本人采用了VOC数据集的格式与树形结构存放图片数据。具体为何要这样处理，在下一节的“在detectron上部署数据集”将会详细说明。
&emsp;&emsp;本节将对上一节使用LabelImgPlus工具标注好的数据需要进一步处理。对于Detectron框架来说，本人采用了VOC数据集的格式与树形结构存放图片数据。
## 0.1 准备工作
&emsp;&emsp;将训练图片放在同一个文件夹中，将测试图片放在同一个文件夹中。
# 1 xml转json
&emsp;&emsp;xml2json.py文件如下：
```
# coding=utf-8
import xml.etree.ElementTree as ET
import os
import json
import collections

coco = dict()
coco['images'] = []
coco['type'] = 'instances'
coco['annotations'] = []
coco['categories'] = []

#category_set = dict()
image_set = set()
image_id = 2019100001  # train:2018xxx; val:2019xxx; test:2020xxx
category_item_id = 1
annotation_id = 1
category_set = ['people', 'bicycle', 'electric bicycle'] #这里填写好检测的类别
'''
def addCatItem(name):
    global category_item_id
    category_item = dict()
    category_item['supercategory'] = 'none'
    category_item_id += 1
    category_item['id'] = category_item_id
    category_item['name'] = name
    coco['categories'].append(category_item)
    category_set[name] = category_item_id
    return category_item_id
'''


def addCatItem(name):
    '''
    增加json格式中的categories部分
    '''
    global category_item_id
    category_item = collections.OrderedDict()
    category_item['supercategory'] = 'none'
    category_item['id'] = category_item_id
    category_item['name'] = name
    coco['categories'].append(category_item)
    category_item_id += 1


def addImgItem(file_name, size):
    global image_id
    if file_name is None:
        raise Exception('Could not find filename tag in xml file.')
    if size['width'] is None:
        raise Exception('Could not find width tag in xml file.')
    if size['height'] is None:
        raise Exception('Could not find height tag in xml file.')
    # image_item = dict()    #按照一定的顺序，这里采用collections.OrderedDict()
    image_item = collections.OrderedDict()
    jpg_name = os.path.splitext(file_name)[0] + '.png'
    image_item['file_name'] = jpg_name
    image_item['width'] = size['width']
    image_item['height'] = size['height']
    image_item['id'] = image_id
    coco['images'].append(image_item)
    image_set.add(jpg_name)
    image_id = image_id + 1
    return image_id


def addAnnoItem(object_name, image_id, category_id, bbox):
    global annotation_id
    #annotation_item = dict()
    annotation_item = collections.OrderedDict()
    annotation_item['segmentation'] = []
    seg = []
    # bbox[] is x,y,w,h
    # left_top
    seg.append(bbox[0])
    seg.append(bbox[1])
    # left_bottom
    seg.append(bbox[0])
    seg.append(bbox[1] + bbox[3])
    # right_bottom
    seg.append(bbox[0] + bbox[2])
    seg.append(bbox[1] + bbox[3])
    # right_top
    seg.append(bbox[0] + bbox[2])
    seg.append(bbox[1])
    annotation_item['segmentation'].append(seg)
    annotation_item['area'] = bbox[2] * bbox[3]
    annotation_item['iscrowd'] = 0
    annotation_item['image_id'] = image_id
    annotation_item['bbox'] = bbox
    annotation_item['category_id'] = category_id
    annotation_item['id'] = annotation_id
    annotation_item['ignore'] = 0
    annotation_id += 1
    coco['annotations'].append(annotation_item)


def parseXmlFiles(xml_path):
    xmllist = os.listdir(xml_path)
    xmllist.sort()
    for f in xmllist:
        if not f.endswith('.xml'):
            continue

        bndbox = dict()
        size = dict()
        current_image_id = None
        current_category_id = None
        file_name = None
        size['width'] = None
        size['height'] = None
        size['depth'] = None

        xml_file = os.path.join(xml_path, f)
        print(xml_file)

        tree = ET.parse(xml_file)
        root = tree.getroot()  # 抓根结点元素

        if root.tag != 'annotation':  # 根节点标签
            raise Exception(
                'pascal voc xml root element should be annotation, rather than {}'.format(root.tag))

        # elem is <folder>, <filename>, <size>, <object>
        for elem in root:
            current_parent = elem.tag
            current_sub = None
            object_name = None

            # elem.tag, elem.attrib，elem.text
            if elem.tag == 'folder':
                continue

            if elem.tag == 'filename':
                file_name = elem.text
                if file_name in category_set:
                    raise Exception('file_name duplicated')

            # add img item only after parse <size> tag
            elif current_image_id is None and file_name is not None and size['width'] is not None:
                if file_name not in image_set:
                    current_image_id = addImgItem(file_name, size)  # 图片信息
                    print('add image with {} and {}'.format(file_name, size))
                else:
                    raise Exception('duplicated image: {}'.format(file_name))
                    # subelem is <width>, <height>, <depth>, <name>, <bndbox>
            for subelem in elem:
                bndbox['xmin'] = None
                bndbox['xmax'] = None
                bndbox['ymin'] = None
                bndbox['ymax'] = None

                current_sub = subelem.tag
                if current_parent == 'object' and subelem.tag == 'name':
                    object_name = subelem.text
                    # if object_name not in category_set:
                    #    current_category_id = addCatItem(object_name)
                    # else:
                    #current_category_id = category_set[object_name]
                    current_category_id = category_set.index(
                        object_name) + 1  # index默认从0开始,但是json文件是从1开始，所以+1
                elif current_parent == 'size':
                    if size[subelem.tag] is not None:
                        raise Exception('xml structure broken at size tag.')
                    size[subelem.tag] = int(subelem.text)

                # option is <xmin>, <ymin>, <xmax>, <ymax>, when subelem is <bndbox>
                for option in subelem:
                    if current_sub == 'bndbox':
                        if bndbox[option.tag] is not None:
                            raise Exception(
                                'xml structure corrupted at bndbox tag.')
                        bndbox[option.tag] = int(option.text)

                # only after parse the <object> tag
                if bndbox['xmin'] is not None:
                    if object_name is None:
                        raise Exception('xml structure broken at bndbox tag')
                    if current_image_id is None:
                        raise Exception('xml structure broken at bndbox tag')
                    if current_category_id is None:
                        raise Exception('xml structure broken at bndbox tag')
                    bbox = []
                    # x
                    bbox.append(bndbox['xmin'])
                    # y
                    bbox.append(bndbox['ymin'])
                    # w
                    bbox.append(bndbox['xmax'] - bndbox['xmin'])
                    # h
                    bbox.append(bndbox['ymax'] - bndbox['ymin'])
                    print(
                        'add annotation with {},{},{},{}'.format(object_name, current_image_id - 1, current_category_id, bbox))
                    addAnnoItem(object_name, current_image_id -
                                1, current_category_id, bbox)
    # categories部分
    for categoryname in category_set:
        addCatItem(categoryname)


if __name__ == '__main__':
    xml_path = 'dataset/train_anatation'
    json_file = 'dataset/train.json'
    # xml_path = 'dataset/test_anatation'
    # json_file = 'dataset/test.json'
    parseXmlFiles(xml_path)
    json.dump(coco, open(json_file, 'w'))

```
&emsp;&emsp;运行好该代码后分别可以得到两个文件：train.json，test.json.
# 2 制作txt文件
&emsp;&emsp;ImageSetsMainCreate.py文件如下：
```
# -*- coding: utf-8 -*-
# @Author: zhiwei
# @Date:   2019-01-31 09:38:58
# @Last Modified by:   zhiwei
# @Last Modified time: 2019-01-31 10:21:58
#
import os
import re

fp1_path = "dataset/ok/JPEGImages"
f = open('dataset/ok/VOCdevkit2007/VOC2007/ImageSets/Main/train.txt', 'w')
s = ""
i = 0
for filename in os.listdir(fp1_path):
    # bicycle_train.txt
    # if re.match('^bicycle.+', filename) != None:
    #     s1 = os.path.splitext(os.path.basename(filename))[0]
    #     s = s + s1 + ' ' + str(1) + "\n"
    # else:
    #     s1 = os.path.splitext(os.path.basename(filename))[0]
    #     s = s + s1 + ' ' + str(0) + "\n"

    # electric bicycle_train.txt
    # if "electric_bicycle" in filename:
    #     s1 = os.path.splitext(os.path.basename(filename))[0]
    #     s = s + s1 + ' ' + str(1) + "\n"
    # else:
    #     s1 = os.path.splitext(os.path.basename(filename))[0]
    #     s = s + s1 + ' ' + str(0) + "\n"

    # train.txt
    s1 = os.path.splitext(os.path.basename(filename))[0]
    s = s + s1 + "\n"

print s

f.write(s)
print len([name for name in os.listdir(fp1_path)])

```
使用该代码生成了8个txt文件，如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190213212657523.png)
文件内容格式如下（以bicycle_test.txt为例，把所有的100张测试图片都囊括）：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190213212817535.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p3X19jaGVu,size_16,color_FFFFFF,t_70)
需要注意的是test.txt和train.txt两个文件的格式如下：（以train.txt为例，一共有200个）
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190213213017196.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p3X19jaGVu,size_16,color_FFFFFF,t_70)
<hr>
第三部分到此结束。如有任何问题，欢迎留言交流学习。
