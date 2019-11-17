---
layout: post
title:  "DirectX：New Project in VS2019"
date:   2019-09-12
excerpt: ""
tag:
- DirectX
---

Tag DirectX下的博客主要用于记录DirectX的学习过程，主要参考《DirectX 12 3D 游戏实战开发》。

# 官方示例工程

在www.d3dcoder.net下载d3d12工程之后便可以基于官方工程构建自己的项目。这里选用的IDE为VS2019，在**新建项目**时选择**C++空项目**，然后把官方示例工程的Common文件夹复制到自己的工程目录，为了测试运行结果，同时把官方工程第六章有关box的`BoxApp.cpp`和`Shaders`文件夹也复制到工程目录，在头文件添加上述的`.h`文件，在源文件添加上述的`.cpp`文件。此时直接开始调试会发现如下报错：

![ERROR_1](https://s2.ax1x.com/2019/11/06/M9HpsH.png)

可以看出，报错内容都是和字符串有关，这是因为字符集设置错误，在`项目属性`-`配置属性`-`高级`下，把字符集设置为**Unicode**再次调试，报错就消失了。但这时又出现了新的错误：

![ERROR_2](https://s2.ax1x.com/2019/11/06/M97xzD.png)

`error LNK2019: 无法解析的外部符号 _main`，`_main`是在链接窗口子系统时引用的一个标识符，而载我们构建空项目时默认链接的是控制台，所以需要在`项目属性`-`配置属性`-`链接器`-`系统`下，把子系统修改为`窗口(/SUBSYSTEM:WINDOWS)`后再开始调试，就可以运行box项目了。

![BOX](https://s2.ax1x.com/2019/11/06/M9HSQe.png)

