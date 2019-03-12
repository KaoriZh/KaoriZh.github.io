layout: post
title:  "Shader Properties"
date:   2019-temp-temp
excerpt: "temp"
tag:

- Shader

- Unity

  ------

  下面给出一些Shader的属性定义方法，具体的属性值以及默认参数可以参考Unity官方文档ShaderLab: Properties。

```
//定义Numbers（数值）和Sliders（区间值）
name ("display name", Range (min, max)) = number
name ("display name", Float) = number
name ("display name", Int) = number
//定义Colors（颜色）和Vectors（向量）
name ("display name", Color) = (number,number,number,number)
name ("display name", Vector) = (number,number,number,number)
//定义Textures（纹理）
name ("display name", 2D) = "defaulttexture" {}
name ("display name", Cube) = "defaulttexture" {}
name ("display name", 3D) = "defaulttexture" {}
```

