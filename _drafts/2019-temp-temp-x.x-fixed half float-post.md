---
layout: post
title:  "temp"
date:   2019-temp-temp
excerpt: "temp"
tag:
- Shader
- Unity

---

- When writing shaders in Cg/HLSL, there are three basic number types: `float`, `half` and `fixed` (as well as vector & matrix variants of them, e.g. half3 and float4x4):

  - `float`: high precision floating point. Generally 32 bits, just like float type in regular programming languages.
  - `half`: medium precision floating point. Generally 16 bits, with a range of –60000 to +60000 and 3.3 decimal digits of precision.
  - `fixed`: low precision fixed point. Generally 11 bits, with a range of –2.0 to +2.0 and 1/256th precision.

  Use lowest precision that is possible; this is especially important on mobile platforms like iOS and Android. Good rules of thumb are:

  - For colors and unit length vectors, use `fixed`.
  - For others, use `half` if range and precision is fine; otherwise use `float`.

  On mobile platforms, the key is to ensure as much as possible stays in low precision in the fragment shader. On most mobile GPUs, applying swizzles to low precision (fixed/lowp) types is costly; converting between fixed/lowp and higher precision types is quite costly as well.