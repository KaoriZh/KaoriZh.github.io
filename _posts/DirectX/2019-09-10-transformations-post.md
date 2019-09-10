---
layout: post
title:  "DirectX：Transformations"
date:   2019-09-10
excerpt: ""
tag:
- DirectX
---

Tag DirectX下的博客主要用于记录DirectX的学习过程，主要参考《DirectX 12 3D 游戏实战开发》。

# Transformations

前面提到了两种变换：仿射变换和坐标变换。仿射变换又包含线性变换。仿射变换和坐标变换的区别在于有没有改变物体本身的属性。坐标系是参考系的一种抽象，物体有没有发生改变也是相对而言的，所以，仿射变换和坐标变换在数学上其实是等价的。对一个物体进行仿射变换得到的结果，也可以是我们更换了参考坐标系得到的观测结果。

因此，我们可以把一个改变几何体的复合变换解释为对应的坐标变换，在之后的学习中会涉及到很多坐标系，如世界坐标系、物体坐标系，而在定义坐标变换矩阵时，通常又是采用仿射变换的角度，所以需要好好理解变换的实质。

# Transformations in DirectX

DirectXMath库提供了与变换有关的函数。

```c++
// 直接指定各轴缩放系数构造缩放变换矩阵
XMMATRIX XM_CALLCONV XMMatrixScaling(float ScaleX, float ScaleY, float ScaleZ);
// 通过向量各分量指定各轴缩放系数构造缩放变换矩阵
// 对应缩放系数为Scale.x、Scale.y、Scale.z
XMMATRIX XM_CALLCONV XMMatrixScalingFromVector(FXMVECTOR Scale);
```

```c++
// 旋转正方向由对应坐标系的对应手定则确定

// 构造绕x轴正方向旋转的旋转变换矩阵
XMMATRIX XM_CALLCONV XMMatrixRotationX(float Angle);
// 构造绕y轴正方向旋转的旋转变换矩阵
XMMATRIX XM_CALLCONV XMMatrixRotationY(float Angle);
// 构造绕z轴正方向旋转的旋转变换矩阵
XMMATRIX XM_CALLCONV XMMatrixRotationZ(float Angle);
// 绕任意轴正方向旋转的旋转变换矩阵
XMMATRIX XM_CALLCONV XMMatrixRotationAxis(FXMVECTOR Axis, float Angle);
```

```c++
// 直接指定各轴偏移量构造平移变换矩阵
XMMATRIX XM_CALLCONV XMMatrixTranslation(float OffsetX, float OffsetY, float OffsetZ);
// 通过向量各分量指定各轴偏移量构造平移变换矩阵
// 对应偏移量为Offset.x、Offset.y、Offset.z
XMMATRIX XM_CALLCONV XMMatrixTranslationFromVector(FXMVECTOR Offset);
```

```c++
// 矩阵M左乘位置向量V，即默认令V.w=1
XMVECTOR XM_CALLCONV XMVector3TransformCoord(FXMVECTOR V, CXMMATRIX M);
// 矩阵M左乘方向向量V，即默认令V.w=0
XMVECTOR XM_CALLCONV XMVector3TransformNormal(FXMVECTOR V, CXMMATRIX M);
```

# 总结

## 缩放

$$
设x、y、z轴的缩放系数为S_x、S_y、S_z，那么缩放矩阵S=
\begin{bmatrix}
S_x&0&0&0\\
0&S_y&0&0\\
0&0&S_z&0\\
0&0&0&1
\end{bmatrix}
$$

根据缩放的性质，把各缩放系数置为**倒数**即得逆矩阵。

## 旋转

$$
\begin{aligned}
&设\vec{n}=(x,y,z)，且|\vec{n}|=1，c=\cos\theta，s=\sin\theta，那么\vec{v}绕\vec{n}正方向旋转\theta的变换表达式为\\
&R_\vec n(\vec v)=proj_\vec n(\vec v)+R_\vec n(\vec v_\perp)
=\cos\theta\vec v+(1-\cos\theta)(\vec n\dot{}\vec v)\vec n+\sin\theta(\vec n\times\vec v)\\
&对应的旋转矩阵R=
\begin{bmatrix}
c+(1-c)x^2&(1-c)xy+sz&(1-c)xz-sy&0\\
(1-c)xy-sz&c+(1-c)y^2&(1-c)yz+sx&0\\
(1-c)xz+sy&(1-c)yz-sx&c+(1-c)z^2&0\\
0&0&0&1
\end{bmatrix}
\end{aligned}
$$

根据旋转矩阵是**正交矩阵**，旋转矩阵的转置矩阵即逆矩阵。

## 平移

$$
设平移的位移为\vec{b}=(b_x,b_y,b_z)，那么平移矩阵T=
\begin{bmatrix}
1&0&0&0\\
0&1&0&0\\
0&0&1&0\\
b_x&b_y&b_z&1
\end{bmatrix}
$$

根据平移的性质，把各偏移量取**相反数**即得逆矩阵。

## 坐标系变换

$$
\begin{aligned}
&设原坐标系为A，待变换坐标系为B。在坐标系B中，原坐标系的原点O_A=(O_x,O_y,O_z)，\\
&原坐标系的基\vec{u}=(u_x,u_y,u_z)，\vec{v}=(v_x,v_y,v_z)，\vec{w}=(w_x,w_y,w_z)\\
&那么坐标系变换矩阵C=
\begin{bmatrix}
u_x&u_y&u_z&0\\
v_x&v_y&v_z&0\\
w_x&w_y&w_z&0\\
O_x&O_y&O_z&1
\end{bmatrix}
\end{aligned}
$$

