---
layout: post
title:  "DirectX：Linear Transformation"
date:   2019-09-09
excerpt: ""
tag:
- DirectX
---

Tag DirectX下的博客主要用于记录DirectX的学习过程，主要参考《DirectX 12 3D 游戏实战开发》。本篇主要是顺着DX12龙书的节奏温习线性代数中的线性变换。

# 线性变换

## 定义

关于线性变换的定义有很多种，这里参考龙书，给出3D图形学的线性变换定义。
$$
数学函数\tau是线性变换当且仅当：\\
\tau(\vec{u}+\vec{v})=\tau(\vec{u})+\tau(\vec{v})\\
\tau(k\vec u)=k\tau(\vec u)
$$

## 矩阵表示法

线性变换是可以用矩阵表示的。
$$
设\vec u=(x,y,z)，\vec u也可以写作\vec u=x\vec i+y\vec j+z\vec k\\
\vec i，\vec j，\vec k为\vec u所在坐标系的单位正交基\\
那么关于\vec u的线性变换\tau也可以写作\tau(\vec u)=x\tau(\vec i)+y\tau(\vec j)+z\tau(\vec k)\\
可以看出这是一种线性组合，可以用矩阵表示为：\\
\tau(\vec u)=x\tau(\vec i)+y\tau(\vec j)+z\tau(\vec k)=[x,y,z]
\begin{bmatrix}\tau(\vec i) \\ \tau(\vec j) \\ \tau(\vec k)\end{bmatrix}
$$

## 缩放

$$
缩放也是一种线性变换。\\
把物体关于x、y、z轴的缩放系数分别记为S_x、S_y、S_z，并记缩放变换为S\\
显然，S(\vec i)=(S_x,0,0)，S(\vec j)=(0,S_y,0)，S(\vec k)=(0,0,S_z)\\
那么缩放矩阵即\begin{bmatrix}S_x&0&0\\ 0&S_y&0\\ 0&0&S_z\end{bmatrix}\\
根据缩放变换的性质我们也很容易得到其逆变换对应的矩阵（即缩放矩阵的逆矩阵）为：\\
\begin{bmatrix}1/S_x&0&0\\ 0&1/S_y&0\\ 0&0&1/S_z\end{bmatrix}
$$

## 旋转

旋转也是一种线性变换。
$$
对于\vec v绕轴\vec n顺时针旋转\theta，\\
为了方便分析，把\vec v正交分解为正交于\vec n的部分\vec v_\perp和平行于\vec n的部分proj_\vec n(\vec v)\\
假设\vec n是单位向量，那么proj_\vec n(\vec v)=({\vec n}\dot{}\vec v)\vec n，\vec v_\perp=\vec v-proj_\vec n(\vec v)\\
记旋转变换为R，显然旋转操作并不会改变proj_\vec n(\vec v)，故R_\vec n(\vec v)=proj_\vec n(\vec v)+R_\vec n(\vec v_\perp)\\
记\vec v和\vec n的夹角为\alpha，那么|\vec n\times\vec v|=|\vec n||\vec v|\sin\alpha=|\vec v|\sin\alpha=|\vec v_\perp|\\
那么R_\vec n(\vec v_\perp)=\vec v_\perp\cos\theta+(\vec n\times\vec v)\sin\theta\\
综上，R_\vec n(\vec v)=proj_\vec n(\vec v)+R_\vec n(\vec v_\perp)\\
=\cos\theta\vec v+(1-\cos\theta)(\vec n\dot{}\vec v)\vec n+\sin\theta(\vec n\times\vec v)\\
把\vec i带入R即可得到R_\vec n(\vec i)=(c+(1-c)x^2,(1-c)xy+sz,(1-c)xz-sy)\\
同理可得R_\vec n(\vec j)和R_\vec n(\vec k)\\
那么旋转矩阵为
\begin{bmatrix}
c+(1-c)x^2&(1-c)xy+sz&(1-c)xz-sy\\
(1-c)xy-sz&c+(1-c)y^2&(1-c)yz+sx\\
(1-c)xz+sy&(1-c)yz-sx&c+(1-c)z^2
\end{bmatrix}\\
其中，c=\cos\theta,s=\sin\theta
$$
旋转矩阵都是正交矩阵，即矩阵中的任两个行向量都互相正交，正交矩阵的转置即逆矩阵。由此我们可以高效得出旋转变换的逆变换对应的矩阵。

特别地，如果选择绕坐标轴旋转，代入标准正交基即可得到绕x、y、z轴旋转对应的矩阵：
$$
R_x=\begin{bmatrix}
1&0&0&0\\
0&\cos\theta&\sin\theta&0\\
0&-\sin\theta&\cos\theta&0\\
0&0&0&1
\end{bmatrix},
R_y=\begin{bmatrix}
\cos\theta&0&-\sin\theta&0\\
0&1&0&0\\
\sin\theta&0&\cos\theta&0\\
0&0&0&1
\end{bmatrix},
R_z=\begin{bmatrix}
\cos\theta&\sin\theta&0&0\\
-\sin\theta&\cos\theta&0&0\\
0&0&1&0\\
0&0&0&1
\end{bmatrix}
$$
这里延伸多一维是为了与仿射变换的矩阵表示统一。

