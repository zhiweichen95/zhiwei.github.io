---
layout:     post              # 使用的布局（不需要改）
title:     Ubuntu美化         # 标题
subtitle:   Powerline support vim & terminal #副标题
date:       2019-03-03        # 时间
author:     zhiwei        # 作者
header-img: img/post-bg-2015.jpg  #这篇文章标题背景图片
catalog: true             # 是否归档
tags:               #标签
    - Ubuntu
---

> 参见 Github: https://github.com/zhiweichen95/vimrc

# 1 安装powerline

```
mkdir -p ~/Documents && cd ~/Documents && git clone https://github.com/zhiweichen95/linux_configure_scripts && cd linux_configure_scripts && bash powerline.sh
```

# 2 安装vim8.0
由于自带或者使用命令```sudo apt install vim```安装的vim不支持python2，而powerline需要python2，所以使用命令卸载原有vim```sudo apt remove vim```，使用以下源码编译安装vim8.0。
```
cd ~/Documents && cd linux_configure_scripts && bash vim_8.0.sh
```

# 3 把Powerline应用到Vim中
在`~/.vimrc`中添加下边语句
```python
    python from powerline.vim import setup as powerline_setup
    python powerline_setup()
    python del powerline_setup
    
    set laststatus=2
```
之后执行命令：`source ~/.vimrc`立即生效vimrc

![](https://wx2.sinaimg.cn/large/007ccxpDly1g1hpgqk66nj30pj0bgq99.jpg)


# 4 把Powerline应用到terminal中

在 ~/.bashrc 中加入以下内容：

```
POWERLINE_SCRIPT=/usr/share/powerline/bindings/bash/powerline.sh
if [ -f $POWERLINE_SCRIPT ]; then
source $POWERLINE_SCRIPT
fi
```

之后执行命令：`source ~/.bashrc`立即生效bashrc

![](https://ws1.sinaimg.cn/large/007ccxpDly1g1hpk377f8j30bk02u0ss.jpg)


本人尝试了在XShell中是欧克的，但是在MobaXterm中会乱码，不知道咋回事，望各位读者指教。
