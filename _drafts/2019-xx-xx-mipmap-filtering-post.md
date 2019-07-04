---
layout: post
title:  "todo"
date:   2019-temp-temp
excerpt: "todo"
tag:
- Unity
- Essay
---

What happens when the pixels of a texture – texels – don't exactly match the pixels they are projected onto? There is a mismatch, which has to be resolved somehow. How this is done is controlled by the *Filter Mode*.

The most straightforward filtering mode is *Point (no filter)*. This means that when a texture is sampled at some UV coordinates, the nearest texel is used. This will give the texture a blocky appearance, unless texels map exactly to display pixels. So it is typically used for pixel-perfect rendering, or when a blocky style is desired.

The default is to use bilinear filtering. When a texture is sampled somewhere in between two texels, those two texels are interpolated. As textures are 2D, this happens both along the U and the V axis. Hence bilinear filtering, not just linear filtering.

This approach works when the texel density is less than the display pixel density, so when you're zooming in to the texture. The result will look blurry. It doesn't work in the opposite case, when you're zooming out of the texture. Adjacent display pixels will end up with samples that are more than one texel apart. This means that parts of the texture will be skipped, which will cause harsh transitions, as if the image was sharpened.

The solution to this problem is to use a smaller texture whenever the texel density becomes too high. The smaller the texture appears on the display, the smaller a version of it should be used. These smaller versions are known as mipmaps and are automatically generated for you. Each successive mipmap has half the width and height of the previous level. So when the original texture size is 512x512, the mip maps are 256x256, 128x128, 64x64, 32x32, 16x16, 8x8, 4x4, and 2x2.

The word *mipmap* is a contraction of *MIP map*. The letters *MIP* stand for the latin phrase *multum in parvo*, which translates to *multitude in a small space*. It was coined by Lance Williams when he first described the mipmapping technique.