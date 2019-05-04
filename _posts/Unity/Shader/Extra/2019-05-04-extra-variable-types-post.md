---
layout: post
title:  "Extra：Variable Types"
date:   2019-05-04
excerpt: "Some may be confusing."
tag:
- Shader
- Unity
- Extra
---

在我们使用Cg或者HLSL进行shader编写的过程中，常常涉及到一些变量类型的使用，其中数字类型有三种最基础的变量类型：`float`，`half`，`fixed`，由此延伸出的还有向量类型以及矩阵类型，如：`float3`，`half4x4`。虽然都是表示数字，但它们之间的差别还是应该清楚，而不是遇到数据就无脑地使用`float`。

- `float`: high precision floating point. Generally 32 bits, just like float type in regular programming languages.
- `half`: medium precision floating point. Generally 16 bits, with a range of –60000 to +60000 and 3.3 decimal digits of precision.
- `fixed`: low precision fixed point. Generally 11 bits, with a range of –2.0 to +2.0 and 1/256th precision.

这是Unity官方的说明。可以看到，从精度上来说，三者的关系是`float`>`half`>`fixed`，而从占用的内存上则必然是相反的，这也就需要我们明智地选取数据类型。

- For colors and unit length vectors, use `fixed`.
- For others, use `half` if range and precision is fine; otherwise use `float`.
- On mobile platforms, the key is to ensure as much as possible stays in low precision in the fragment shader. On most mobile GPUs, applying swizzles to low precision (fixed/lowp) types is costly; converting between fixed/lowp and higher precision types is quite costly as well.

同样，这是来自官方的建议。对于颜色和单位向量，使用`fixed`，这是因为1/256已经足以满足颜色的精度需求。其他情况下，在精度要求较低时使用`half`，否则使用`float`。在移动平台，尤其要注意float类型的使用，一是因为移动平台的精度要求更低，二是因为移动平台的内存更有限。在移动平台，还要注意，尽量避免`swizzles`操作以及高低精度类型的转换（`fixed`/`half`--`float`），这两种操作的代价都是比较昂贵的。对于大多数现代PC来说，`float`类型的内存占用是可以被接受的，`float`的精度相较于`half`也比较能满足精度的需求。

此外，还有一种关键字是`uniform`，它的作用是表示这个变量是全局可变变量。在着色器中使用这个变量时，这个变量会被添加到着色器的uniform变量列表中，在Cg/HLSL中，全局变量都是默认带`uniform`的，在Shaderlab中也是如此。因此我们可以在代码中使用`uniform`标记变量，但这通常是多余的。