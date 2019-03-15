---
layout: post
title:  "Occluder/Occludee Static"
date:   2019-temp-temp
excerpt: "Description about Occluder/Occludee Static in Inspector Static Tags"
tag:
- Unity
- Occlusion Culling
- Essay
---

​	关于物体Inspector窗口的Static属性中的 **Occluder Static** 和 **Occludee Static **选项的区别。

> When should you use **Occludee Static**? Completely transparent or translucent objects that do not occlude, as well as small objects that are unlikely to occlude other things, should be marked as **Occludees**, but not **Occluders**. This means they will be considered in occlusion by other objects, but will not be considered as occluders themselves, which will help reduce computation.

​	这是官方文档（unity3D/Editor/Data/en/Manual/OcclusionCulling.html）所给的描述。

​	也就是说，对于完全透明或者半透明且不会遮挡到其他物体的物体，当然也包括那些非常小的、不太可能遮挡其他物体的物体，对于这两类，我们可以将它们标记为**Occludees**。这意味着他会被纳入其他物体的遮挡检测考虑范围，但自身并不会去检测是否遮挡物体。

