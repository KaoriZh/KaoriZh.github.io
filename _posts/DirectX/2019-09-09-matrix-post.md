---
layout: post
title:  "DirectX：Matrix"
date:   2019-09-09
excerpt: ""
tag:
- DirectX
---

Tag DirectX下的博客主要用于记录DirectX的学习过程，主要参考《DirectX 12 3D 游戏实战开发》。

# Matrix in DirectX

3D数学最重要的就是描述几何体的变换，其中肯定会涉及到矩阵。本篇的主要内容即是DirectXMath库中与矩阵相关的部分。 

## 矩阵

首先温习矩阵运算的几个特点。

- 矩阵加法满足交换律和结合律
- 矩阵A、B相乘有意义，当且仅当A的列数与B的行数相同
- 矩阵乘法一般不满足交换律
- 标量乘法对矩阵加法、矩阵乘法对标量加法满足分配律
- 向量与矩阵相乘实际上是一种线性组合
- 单位矩阵即主对角线均为一、其余为零的方阵
- 方阵可逆当且仅当方阵的行列式不等于零
- 一个方阵关于i行j列的余子式即该方阵去掉第i行和第j列剩下的部分构成的方阵，代数余子式即在余子式的基础上乘上-1的i+j次方
- 把方阵关于i行j列的余子式都放到对应的行和列上构成的矩阵称为方阵的代数余子式矩阵，该方阵的伴随矩阵即为代数余子式矩阵的转置
- 方阵的逆矩阵为该方阵的伴随矩阵除以其行列式

## DirectXMath库中的矩阵类型

在3D图形学中，通常最多涉及到4维的内容，对应4维向量和4*4的矩阵。DirectXMath库提供了`XMMATRIX`类型来表示矩阵。

```c++
#if (defined(_M_IX86) || defined(_M_X64) || defined(_M_ARM)) && defined(_XM_NO_INTRINSICS)
struct XMMATRIX
#else
__declspec(align(16)) struct XMMATRIX
#endif
{
    XMVECTOR R[4];
    
    XMMATRIX() {}
    
    XMMATRIX(FXMVECTOR R0, FXMVECTOR R1, FXMVECTOR R2, CXMVECTOR R3)
    { r[0] = R0; r[1] = R1; r[2] = R2; r[3] = R3; }
    
    XMMATRIX(float m00, float m01, float m02, float m03,
            float m10, float m11, float m12, float m13,
            float m20, float m21, float m22, float m23,
            float m30, float m31, float m32, float m33);
    
	explicit XMMATRIX(_In_reads_(16) const float *pArray);
    
    XMMATRIX& operator= (const XMMATRIX& M)
    { r[0] = M.r[0]; r[1] = M.r[1]; r[2] = M.r[2]; r[3] = M.r[3];
    return *this; }
}
```

可以看到，DirectXMath库使用结构体`XMMATRIX`表示矩阵，其中包括4个4维向量`XMVECTOR`，借此使用SIMD技术。在构造矩阵时，可以通过其他矩阵生成，也可以通过4个4维向量构造，还可以通过一个长度为16的数组或者直接使用16个`float`构造。

## 加载/存储

与`XMFLOATn`类似，矩阵也对应着一个存储结构体`XMFLOAT4X4`。

```c++
struct XMFLOAT4X4
{
    union
    {
        struct
        {
            float _11, _12, _13, _14;
            float _21, _22, _23, _24;
            float _31, _32, _33, _34;
            float _41, _42, _43, _44;
        };
        float m[4][4];
    };
    
    XMFLOAT4X4() {}
    
    XMFLOAT4X4(float m00, float m01, float m02, float m03,
            float m10, float m11, float m12, float m13,
            float m20, float m21, float m22, float m23,
            float m30, float m31, float m32, float m33);
    
    explicit XMFLOAT4X4(_In_reads_(16) const float *pArray);
    
    float operator() (size_t Row, size_t Column) const
    { return m[Row][Column]; }
    
    float& operator() (size_t Row, size_t Column)
    { return m[Row][Column]; }
    
    XMFLOAT4X4& operator= (const XMFLOAT4X4& Float4x4);
};
```

使用下面的方法可以加载/存储矩阵。

```c++
inline XMMATRIX XM_CALLCONV XMLoadFloat4x4(const XMFLOAT4X4* pSource);
inline void XM_CALLCONV XMStoreFloat4x4(XMFLOAT4X4* pDestination, FXMMATRIX M);
```

## 参数传递

在声明具有`XMMATRIX`类型的函数时，应把一个`XMMATRIX`视为4个`XMVECTOR`。在传入的`XMVECTOR`不超过2个时，第一个`XMMATRIX`应声明为`FXMVECTOR`，其余均为`CXMMATRIX`。这些同样由约定注解`XM_CALLCONV`支持。同样地，构造函数是一个特例，在构造函数中总是采用`CXMMATRIX`类型获取`XMMATRIX`参数，且不要使用`XM_CALLCONV`注解。

```c++
// 在32位的Windows系统上，XM_CALLCONV转化为__fastcall调用约定
// __fastcall调用约定通过寄存器传递前3个XMVECTOR参数，但由于
// 1个XMMATRIX类型包含4个XMVECTOR，故此时所有的XMMATRIX都只能通过堆栈引用
typedef const XMMATRIX& FXMMATRIX;
typedef const XMMATRIX& CXMMATRIX;
```

## 运算符重载

`XMMATRIX`类型提供了有关矩阵运算的运算符重载。

```c++
XMMATRIX operator+ () const { return *this; }
XMMATRIX operator- () const;

XMMATRIX& XM_CALLCONV operator+= (FXMMATRIX M);
XMMATRIX& XM_CALLCONV operator-= (FXMMATRIX M);
XMMATRIX& XM_CALLCONV operator*= (FXMMATRIX M);

XMMATRIX& operator*= (float S);
XMMATRIX& operator/= (float S);

XMMATRIX XM_CALLCONV operator+ ( FXMMATRIX M) const;
XMMATRIX XM_CALLCONV operator- ( FXMMATRIX M) const;
XMMATRIX XM_CALLCONV operator* ( FXMMATRIX M) const;

XMMATRIX& operator*= (float S) const;
XMMATRIX& operator/= (float S) const;

friend XMMATRIX XM_CALLCONV operator* (float S, FXMMATRIX M);
```

## 矩阵函数

DirectXMath库还包含了矩阵性质相关的实用函数。

```c++
// 单位矩阵
XMMATRIX XM_CALLCONV XMMatrixIdentity();

// 判断是否是单位矩阵
bool XM_CALLCONV XMMatrixIsIdentity(FXMMATRIX M);

// 矩阵乘法
XMMATRIX XM_CALLCONV XMMatrixMultiply(FXMMATRIX A, CXMMATRIX B);

// 求转置
XMMATRIX XM_CALLCONV XMMatrixTranspose(FXMMATRIX M);

// 求行列式，返回的XMVECTOR的每一个分量都是行列式值
XMVECTOR XM_CALLCONV XMMatrixDeterminant(FXMMATRIX M);

// 求逆矩阵，其中输入的pDeterminant为M的行列式
XMMATRIX XM_CALLCONV XMMatrixInverse(XMVECTOR* pDeterminant, FXMMATRIX M);
```

