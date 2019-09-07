---
layout: post
title:  "Extra：Cg Math Functions"
date:   2019-05-04
excerpt: ""
tag:
- Shader
- Unity
- Extra
- Cg Math Functions
---

# 常用[Cg函数](https://developer.download.nvidia.cn/cg/index_stdlib.html)

## 数学函数

`abs（x）`：绝对值

```C
// float类型的实现
float abs(float x) {
    return max(-a, a);
}
```

`sin（x）`：正弦，输入为弧度

```C
// float类型的实现
float sin(float a) {
  /* C simulation gives a max absolute error of less than 1.8e-7 */
  float4 c0 = float4( 0.0,            0.5,
                      1.0,            0.0            );
  float4 c1 = float4( 0.25,          -9.0,
                      0.75,           0.159154943091 );
  float4 c2 = float4( 24.9808039603, -24.9808039603,
                     -60.1458091736,  60.1458091736  );
  float4 c3 = float4( 85.4537887573, -85.4537887573,
                     -64.9393539429,  64.9393539429  );
  float4 c4 = float4( 19.7392082214, -19.7392082214,
                     -1.0,            1.0            );

  /* r0.x = sin(a) */
  float3 r0, r1, r2;

  r1.x  = c1.w * a - c1.x;                // only difference from cos!
  r1.y  = frac( r1.x );                   // and extract fraction
  r2.x  = (float) ( r1.y < c1.x );        // range check: 0.0 to 0.25
  r2.yz = (float2) ( r1.yy >= c1.yz );    // range check: 0.75 to 1.0
  r2.y  = dot( r2, c4.zwz );              // range check: 0.25 to 0.75
  r0    = c0.xyz - r1.yyy;                // range centering
  r0    = r0 * r0;
  r1    = c2.xyx * r0 + c2.zwz;           // start power series
  r1    =     r1 * r0 + c3.xyx;
  r1    =     r1 * r0 + c3.zwz;
  r1    =     r1 * r0 + c4.xyx;
  r1    =     r1 * r0 + c4.zwz;
  r0.x  = dot( r1, -r2 );                 // range extract

  return r0.x;
}
```

`cos（x）`：正弦，输入为弧度

```C
// float类型的实现
float cos(float a) {
  /* C simulation gives a max absolute error of less than 1.8e-7 */
  const float4 c0 = float4( 0.0,            0.5,
                            1.0,            0.0            );
  const float4 c1 = float4( 0.25,          -9.0,
                            0.75,           0.159154943091 );
  const float4 c2 = float4( 24.9808039603, -24.9808039603,
                           -60.1458091736,  60.1458091736  );
  const float4 c3 = float4( 85.4537887573, -85.4537887573,
                           -64.9393539429,  64.9393539429  );
  const float4 c4 = float4( 19.7392082214, -19.7392082214,
                           -1.0,            1.0            );

  /* r0.x = cos(a) */
  float3 r0, r1, r2;

  r1.x  = c1.w * a;                       // normalize input
  r1.y  = frac( r1.x );                   // and extract fraction
  r2.x  = (float) ( r1.y < c1.x );        // range check: 0.0 to 0.25
  r2.yz = (float2) ( r1.yy >= c1.yz );    // range check: 0.75 to 1.0
  r2.y  = dot( r2, c4.zwz );              // range check: 0.25 to 0.75
  r0    = c0.xyz - r1.yyy;                // range centering
  r0    = r0 * r0;
  r1    = c2.xyx * r0 + c2.zwz;           // start power series
  r1    =     r1 * r0 + c3.xyx;
  r1    =     r1 * r0 + c3.zwz;
  r1    =     r1 * r0 + c4.xyx;
  r1    =     r1 * r0 + c4.zwz;
  r0.x  = dot( r1, -r2 );                 // range extract

  return r0.x;
}
```

`sincos（x，out s，out c）`：s=sin（x），c=cos（x）

`ceil（x）`：向上取整

`floor（x）`：向下取整（floor（-1.3）= -2）

`round（x）`：四舍五入

`frac（x）`：取x的小数部分

`clamp（x，a，b）`：把x截取到[a，b]

`saturate（x）`：把x截取到[0，1]

`lerp（a，b，f）`：（1-f） * a + f * b

`step（a，x）`：返回x>=a

`smoothstep（min，max，x）`：x=min时返回0，x=max时返回1；否则返回下式的值
$$
-2*\left(x-min\over max-min\right)^3+3*\left(x-min\over max-min\right)^2
$$
`pow（x，y）`：计算x的y次方

`sqrt（x）`：计算x的算术平方根

`noise（x）`：返回根据x生成的伪随机数，范围[0，1]

`min（a，b）`：取最小

`max（a，b）`：取最大

`normalize（x）`：把x化为单位向量

`length（x）`：返回向量x的模

`distance（x，y）`：计算x，y的欧氏距离

`dot（a，b）`：点积

`cross（a，b）`：叉积

`mul（a，b）`：乘法

## 光照函数

光照函数的输入向量都必须归一化，入射光方向均指从外指向顶点的方向

`reflect（I，N）`：反射函数，I为入射光向量，N为反射表面的法向量，返回反射光向量

```C
float3 reflect(float3 i, float3 n) {
  return i - 2.0 * n * dot(n, i);
}
```

`refract（I，N，eta）`：折射函数，I为入射光向量，N为反射表面的法向量，eta是介质折射率，返回折射光向量

```C
float3 refract(float3 i, float3 n, float eta) {
  float cosi = dot(-i, n);
  float cost2 = 1.0f - eta * eta * (1.0f - cosi * cosi);
  float3 t = eta * i + ((eta * cosi - sqrt(abs(cost2))) * n);
  return t * (float3)(cost2 > 0);
}
```

## 纹理采样函数

`tex2D（sampler2D samp，float2 s）`：samp是待采样的纹理，s是纹理坐标