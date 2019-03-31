---
layout: post
title:  "tmp"
date:   2019-xx-xx
excerpt: "tmp"
tag:
- Shader
- Unity
---

1. World Matrix - Transforms 3D data from Model Space into World Space. You need to set this before rendering every entity in your world.
2. View Matrix - Transforms from World Space into View Space. You need to set this each time your camera changes position
3. Projection Matrix - Transforms from View Space into Screen Space. You normally set this just once during initialization