---
layout: post
title:  "Computer Organization-the Representation of Number"
date:   2019-05-23
excerpt: ""
tag:
- Computer Organization
---

# 机器数的表示

## 1.定点表示

所有数据的**小数点位置固定不变**（在符号位与数值之间或者位于数值之后，分别对应定点小数（纯小数）和整数），且小数点是隐含表示的，不占用位。现代计算机一般采用浮点方式表示小数。

## 2.浮点表示

让小数点的位置**根据需要浮动**。
$$
N=M\times r^E
$$
N为真值，M为尾数，E是阶码，r是底数。尾数一般为纯小数，常用原码或补码表示；阶码为纯整数，常用移码或补码表示。

![Floating-point.png](https://github.com/KaoriZh/KaoriZh.github.io/blob/master/_Images/Computer%20Organization/Floating-point.png?raw=true)

### 规格化的浮点数

即浮点数的科学计数法。为了提高运算的精度，充分利用位数的有效数位，浮点数通常表示为规格化形式。

例：对于二进制数0.0001101，可以表示为
$$
0.001101\times 2^{-01}，0.01101\times 2^{-10}，0.1101\times 2^{-11}
$$
其中，最后一个才是规格化的浮点表示，即尾数的最高位必须是有效值。

### 溢出

数值大于所能表示的最大正数称为**正上溢**，小于所能表示的绝对值最大负数称为**负上溢**，数据产生上溢时应停止运算操作进行溢出处理。数值位于0到规格化最小正数之间称为**正下溢**，位于0到规格化绝对值最小负数之间称为**负下溢**，产生下溢时计算机一般不做处理，将数值置为机器零即可。

## 3.移码表示法

**浮点数阶码是带符号的定点整数，通常采用移码表示。**

### 定义

**移码**（增码、偏码）：在真值X基础上加一个常数（称为偏置值），相当于X在数轴上向正方向偏移了若干单位。

例：X = 1101101，偏置值为2^7。
$$
[X]_移=2^7+(1101101)_2={\bf{1}}1101101，[X]_补={\bf{0}}1101101
$$
X=-1101101，偏置值为2^7。
$$
[X]_移=2^7+(-1101101)_2=10000000-1101101={\bf{0}}0010011，[X]_补={\bf{1}}0010011
$$

### 特点

1.移码的符号位（最高位）为0负1正，与另外三种码制相反。

2.移码可以直观反应真值的大小，全为0时真值最小，全为1时真值最大，有助于浮点数阶码的大小比较。

3.移码0的表示形式唯一：1000。

4.移码把真值映射到正数域，即移码可视为无符号数，可以按无符号数规则比较大小。

5.同一数值的移码和补码除最高位相反外，其他各位相同。

### 浮点数阶码采用移码表示的原因

1.便于比较，由于规定了规格化表示，阶码的大小可以反应浮点值的大小。

2.阶码全为0时，尾数也全为0，简化机器的判零电路。

## 4.浮点数尾数的基数

基数r越大表示范围越大，精度越小，运算中精度损失越小，运算中的移位次数越少（运算速度越高）。

## 5.IEEE754标准浮点数

目前常用的80x86系列微型机采用的标准，第一位为数符，中间若干位为移码表示的阶码，余下的部分是原码表示的尾数。下表是IEEE754标准中的3种浮点数。

|    类型    | 数符（位） | 阶码（位） | 尾数（位） | 总位数 | 偏置值（16进制/10进制） |
| :--------: | :--------: | :--------: | :--------: | :----: | :---------------------: |
|  短浮点数  |     1      |     8      |     23     |   32   |    7FH / 127(2^8-1)     |
|  长浮点数  |     1      |     11     |     52     |   64   |       3FFH / 1023       |
| 临时浮点数 |     1      |     15     |     64     |   80   | 3FFFH / 16383（2^14-1） |

短浮点数和长浮点数又分别称为单精度和高精度浮点数，它们都隐含了尾数的最高数位（以2为基数的规格化浮点数的尾数恒为1，故隐含），临时浮点数又称扩展精度浮点数，它没有隐含位。

例：十进制数100.25转换成短浮点数。
$$
（100.25）_{10}=(1100100.01)_2=(1.10010001)_2\times 2^{(110)_2}
$$

$$
符号位为0
$$

$$
阶码的移码为111,1111+110=1000,0101
$$

$$
尾数为100,1000,1000,0000,0000,0000
$$

短浮点数C1C90000H转换成十进制数。
$$
(C1C90000)_{16}=(1100,0001,1100,1001,0000,0000,0000,0000)_2
$$

$$
符号位为1
$$

$$
阶码的移码为1000,0011，减去偏置值，阶码真值：1000,0011-111,1111=100
$$

$$
尾数为100,1001,0000,0000,0000,0000，即(1.1001001)_2
$$

$$
规格化形式为(1.1001001)_2\times 2^{(100)_2}，即(11001.001)_2=(25.125)_{10}
$$

$$
加上符号位，该数的十进制表示为-25.125
$$

## 6.定点、浮点表示法的区别（字长相同情况下）

1.数值范围：浮点远大于定点。

2.精度：浮点的表示范围大是以牺牲精度为前提的。

3.运算：浮点运算要复杂许多。

4.溢出处理：定点数为普通溢出，浮点数只有在尾数和阶码都超出表示范围时才溢出。