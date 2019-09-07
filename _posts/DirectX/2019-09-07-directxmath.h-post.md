---
layout: post
title:  "DirectX：DirectXMath.h"
date:   2019-09-07
excerpt: ""
tag:
- DirectX
---

Tag DirectX下的博客主要用于记录DirectX的学习过程，主要参考《DirectX 12 3D 游戏实战开发》。

# Math in DirectX

Shader的编写离不开数学运算，尤其是向量矩阵类型的运算，DirectX为我们准备了一个完备的数学库`DirectXMath.h`，里面封装了常用的向量矩阵类型，并且重载了许多方法，如点积、叉积、矩阵乘法等。DirectXMath是Windows SDK的一部分，是为D3D打造的3D数学库，采用SIMD指令集加速向量运算，如：单条SIMD加法指令可以直接计算4D向量的加法结果。

## 变量类型

添加`#include <DirectXMath.h>`即可使用DirectXMath库，其中的代码都位于`namespace DirectX`，使用相关数据类型则需添加`#include <DirectXPackedVector.h>`，其中的代码位于`namespace DirectX::PackedVector`。在DirectXMath库中的核心类型为`XMVECTOR`，它需要按16字节对齐，其中向量类型为`XMFLOAT2`、`XMFLOAT3`、`XMFLOAT4`。而在开启SSE2之后，在x86和x64平台中，`XMVECTOR`被定义为一个共用体`_m128`，通过这个类型，SIMD技术才得以实现。

```c++
// _m128在xmmintrin.h下被定义
typedef union __declspec(intrin_type) __declspec(align(16)) __m128 {
     float               m128_f32[4];
     unsigned __int64    m128_u64[2];
     __int8              m128_i8[16];
     __int16             m128_i16[8];
     __int32             m128_i32[4];
     __int64             m128_i64[2];
     unsigned __int8     m128_u8[16];
     unsigned __int16    m128_u16[8];
     unsigned __int32    m128_u32[4];
 } __m128;
```

## Setter函数

DirectXMath库提供了设置`XMVECTOR`类型中数据的函数。

```c++
// return zero vector
XMVECTOR XM_CALLCONV XMVectorZero();

// return unit vector
XMVECTOR XM_CALLCONV XMVectorSplatOne();

// return vector(x, y, z, w)
XMVECTOR XM_CALLCONV XMVectorSet(float x, float y, float z, float w);

// return vector(Value, Value, Value, Value)
XMVECTOR XM_CALLCONV XMVectorReplicate(float Value);

// return vector(V.x, V.x, V.x, V.x)
XMVECTOR XM_CALLCONV XMVectorSplatX(FXMVECTOR V);

// return vector(V.y, V.y, V.y, V.y)
XMVECTOR XM_CALLCONV XMVectorSplatY(FXMVECTOR V);

// return vector(V.z, V.z, V.z, V.z)
XMVECTOR XM_CALLCONV XMVectorSplatZ(FXMVECTOR V);
```

## 常向量

`XMVECTOR`类型的常量应使用`XMVECTORF32`或者`XMVECTORU32`类型表示，即使用`XMVECTORF32`初始化浮点型向量，使用`XMVECTORU32`初始化整型向量。

## 加载/存储

为了发挥SIMD特性，在构建向量实例时还应使用库提供的loading function把实例转换为`XMVECTOR`类型，同时，库还提供了逆向转换的storage function，用于把`XMVECTOR`转换为`XMFLOATn`类型。总结就是：变量统一为`XMVECTOR`类型，类内成员用`XMFLOATn`类型，运算前转换为`XMVECTOR`类型，运算后转换回`XMFLOATn`类型。

```c++
// loading function（n为向量维度）
XMVECTOR XM_CALLCONV XMLoadFloatn(const XMFLOATn *pSource);
// storage function（n为向量维度）
void XM_CALLCONV XMStoreFloatn(XMFLOATn *pDestination, FXMVECTOR V);

// 获取or存储XMVECTOR中的某个分量(以x分量为例)
float XM_CALLCONV XMVectorGetX(FXMVECTOR V);
XMVECTOR XM_CALLCONV XMVectorSetX(FXMVECTOR V, float x);
```

## 参数传递

为了进一步提高效率，DirectX还允许把`XMVECTOR`类型的值作为函数参数直接送入SSE寄存器中，但是通过这种方法传递的参数有数量限制，数量的限制也因平台而异。为了让代码更具通用性，DirectX为我们提供了一种约定注解`XM_CALLCONV`。这个注解添加在有`XMVECTOR`类型参与的函数定义之前，编译器会根据平台转化为特定的调用约定。调用约定则会把特定数量的`XMVECTOR`类型直接送往寄存器。

```c++
// 在32位的Windows系统上，XM_CALLCONV转化为__fastcall调用约定
// 把前三个XMVECTOR类型直接传入寄存器时，将会出现如下等类型定义
typedef const XMVECTOR FXMVECTOR;
typedef const XMVECTOR& GXMVECTOR;
typedef const XMVECTOR& HXMVECTOR;
typedef const XMVECTOR& CXMVECTOR;
```

DirectX给我们的建议是，在同一个函数调用中，前三个`XMVECTOR`参数应使用类型`FXMVECTOR`，第四个`XMVECTOR`参数应使用`GXMVECTOR`，第5、6个`XMVECTOR`应使用`HXMVECTOR`，其余的使用`CXMVECTOR`。而在定义构造函数时，规则又有所不同。定义构造函数时，前三个`XMVECTOR`类型应使用`FXMVECTOR`类型，其余的使用`CXMVECTOR`，而且不要对构造函数使用XM_CALLCONV注解。而在参数输出时并不会输出到SSE寄存器内，故处理方式和普通类型一致。在DirectXMath库某些函数的定义中就可以体会到`XMVECTOR`的参数传递法则。

```c++
// DirectXMath库中的XMMatrixTransformation函数
inline XMMATRIX XM_CALLCONV XMMatrixTransformation(
	FXMVECTOR ScalingOrigin,
	FXMVECTOR ScalingOrientationQuaternion,
	FXMVECTOR Scaling,
	GXMVECTOR RotationOrigin,
	HXMVECTOR RotationQuaternion,
	HXMVECTOR Translation);
```

## 运算符重载

`XMVECTOR`类型提供了有关向量间运算、向量与标量运算的运算符重载。

```c++
XMVECTOR XM_CALLCONV operator+ (FXMVECTOR V);
XMVECTOR XM_CALLCONV operator- (FXMVECTOR V);

XMVECTOR& XM_CALLCONV operator+= (XMVECTOR& V1, FXMVECTOR V2);
XMVECTOR& XM_CALLCONV operator-= (XMVECTOR& V1, FXMVECTOR V2);
XMVECTOR& XM_CALLCONV operator*= (XMVECTOR& V1, FXMVECTOR V2);
XMVECTOR& XM_CALLCONV operator/= (XMVECTOR& V1, FXMVECTOR V2);

XMVECTOR& operator*= (XMVECTOR& V, float S);
XMVECTOR& operator/= (XMVECTOR& V, float S);

XMVECTOR XM_CALLCONV operator+ (FXMVECTOR V1, FXMVECTOR V2);
XMVECTOR XM_CALLCONV operator- (FXMVECTOR V1, FXMVECTOR V2);
XMVECTOR XM_CALLCONV operator* (FXMVECTOR V1, FXMVECTOR V2);
XMVECTOR XM_CALLCONV operator/ (FXMVECTOR V1, FXMVECTOR V2);
XMVECTOR XM_CALLCONV operator* (FXMVECTOR V, float S);
XMVECTOR XM_CALLCONV operator* (float S, FXMVECTOR V);
XMVECTOR XM_CALLCONV operator/ (FXMVECTOR V, float S);
```

## 向量函数

DirectXMath库提供了封装好的函数执行向量运算，下面给出其中部分的3D向量版本，对应的有2D和4D版本，函数名形式相同。每个函数的命名都已经很好地体现了函数的功能。可以注意到，即使运算在数学上的计算结果是标量，例如点积，对应函数的返回类型仍然是`XMVECTOR`，而这个结果标量会被复制到各个分量中。这么做的原因之一是尽量减少SIMD向量和标量的混合运算，提高效率。DirectXMath库还提供了一些性能开销交稿的函数的估算版本，这些函数精度低，速度快。

```c++
XMVECTOR XM_CALLCONV XMVector3Length(FXMVECTOR V); // 计算V的模长
XMVECTOR XM_CALLCONV XMVector3LengthSq(FXMVECTOR V); // 计算V模长的平方
XMVECTOR XM_CALLCONV XMVector3Dot(FXMVECTOR V1, FXMVECTOR V2); // 点积
XMVECTOR XM_CALLCONV XMVector3Cross(FXMVECTOR V1, FXMVECTOR V2); // 叉积
XMVECTOR XM_CALLCONV XMVector3Normalize(FXMVECTOR V); // 规范化
XMVECTOR XM_CALLCONV XMVector3Orthogonal(FXMVECTOR V); // 返回一个与V正交的向量
XMVECTOR XM_CALLCONV XMVector3AngleBetweenVectors(FXMVECTOR V1, FXMVECTOR V2); // 计算向量间的夹角

// 计算V关于Normal的平行分量和正交分量，输入的Normal必须是已经normalize的
// pParallel存储平行分量，pPerpendicular存储正交分量
void XM_CALLCONV XMVector3ComponentsFromNormal(
	XMVECTOR* pParallel,
	XMVECTOR* pPerpendicular,
	FXMVECTOR V,
	FXMVECTOR Normal
);

bool XM_CALLCONV XMVector3Equal(FXMVECTOR V1, FXMVECTOR V2); // 判断V1和V2是否相同
bool XM_CALLCONV XMVector3NotEqual(FXMVECTOR V1, FXMVECTOR V2); // 判断V1和V2是否不同
// 判断V1和V2是否近似相等，容差值为Epsilon，这是为了弥补浮点误差
XMFINLINE bool XM_CALLCONV XMVector3NearEquals(FXMVECTOR V1, FXMVECTOR V2, FXMVECTOR Epsilon);

XMVECTOR XM_CALLCONV XMVector3LengthEst(FXMVECTOR V); // 估算V的模长
XMVECTOR XM_CALLCONV XMVector3NormalizeEst(FXMVECTOR V); // 估算V的规范化向量
```

## 杂项

DirectXMath库也提供了一些数学常量的近似值，以及弧度角度转化、比较值的函数。

```c++
const float XM_PI = 3.141592654f;
const float XM_2PI = 6.283185307f;
const float XM_1DIVPI = 0.318309886f;
const float XM_1DIV2PI = 0.159154943f;
const float XM_PIDIV2 = 1.570796327f;
const float XM_PIDIV4 = 0.785398163f;

inline float XMConvertToRadians(float fDegrees)
{ return fDegrees * (XM_PI / 180.0f); }
inline float XMConvertToDegrees(float fRadians)
{ return fRadians * (180.0f / XM_PI); }

template<class T> inline T XMMin(T a, T b) { return (a < b) ? a : b; }
template<class T> inline T XMMax(T a, T b) { return (a > b) ? a : b; }
```

