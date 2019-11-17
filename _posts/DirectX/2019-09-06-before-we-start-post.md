---
layout: post
title:  "DirectX：Before We Start"
date:   2019-09-06
excerpt: ""
tag:
- DirectX
---

Tag DirectX下的博客主要用于记录DirectX的学习过程，主要参考《DirectX 12 3D 游戏实战开发》。

# 开始前的准备

DirectX早在Win8及以上的版本中集成到了Windows SDK中，DirectX库采用的指令集为SIMD SSE2。因此，在每次构建DirectX工程时，我们都需要做一些配置，以便发挥这套指令集的性能优势。

我们通常采用Visual Studio作为开发环境，而这里，我选择了VS2019，在安装的时候，勾选`C++游戏开发`，选择其中的`C++分析工具`以及最新的`Windows10 SDK`即可。创建项目时，选择基于C++的空项目，打开项目后，首先进入`项目属性`，在`配置属性`的`高级`栏目下，字符集设置选择`使用Unicode字符集`；`C/C++`栏目的`代码生成`下，选择启用增强指令集`流式处理SIMD扩展2（/arch：SSE2）`，浮点模型选择`快速`。