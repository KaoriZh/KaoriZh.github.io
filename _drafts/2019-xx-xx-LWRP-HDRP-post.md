---
layout: post
title:  "LWRP/HDRP"
date:   2019-temp-temp
excerpt: "temp"
tag:
- Shader
- Unity
- LWRP/HDRP
---

# 如何给一个项目加入LWRP/HDRP

​	首先，在 **Window** -- **Package Manager** 找到 High Definition RP，install（若没有找到，则在上方 **Advanced** 勾选 **Show Preview** ）。

​	然后在工程选择 **Project** -- **Assets** -- **Create** -- **Rendering** -- **LWRP/HDRP** 创建一个Render Pipeline。单击刚创建的RP文件，在 **Inspector ** 窗口可以看到关于该RP的各种属性。为了应用该管线，我们在上方菜单选择 **Edit** -- **Project Setting** -- **Graphics** ，将刚创建的RP文件拖拽到 **Scriptable Render Pipeline Settings** ，这样就为工程配置好了渲染管线。然后我们在 **Project Setting** -- **Player** -- **Other Settings** 把 **Color Space** 切换为 **Linear** ，这是因为 **Physically Based Rendering** （基于物理的渲染）需要在 **Linear Color Space** （线性色彩空间）进行。

​	但是我们会发现，我们场景内的所有Object都丢失了材质。这是因为，原本在普通模式下的Shader不适用于LWRP/HDRP，所以，我们需要为每个材质配置支持的Shader类型。找到需要变更的材质，将Shader设置为HDRP（LWRP）下的Shader即可正常显示。由于逐个配置过于麻烦，Unity为我们提供了便捷方法，我们选择上方菜单 **Edit** -- **Upgrade Project Materials to High Definition(Lightweight) Materials** , Unity就会自动帮我们配置好。