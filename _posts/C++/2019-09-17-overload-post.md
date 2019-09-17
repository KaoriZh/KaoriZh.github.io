---
layout: post
title:  "C++：Overload"
date:   2019-09-17
excerpt: ""
tag:
- C++
- OOP
---

# Overload

# 重载

函数签名是指函数的参数个数，参数类型以及参数的顺序。重载的定义是：**在同一作用域内函数签名不同但函数名相同的函数互为重载**。

```c++
// 以下几个函数互为重载
void print();
void print(int a);
void print(double a, int b);
void print(int a, double b);
void print(string a, double b);
```

参数的不同也可以是通过`const`区分的。

```c++
// 以下两个函数同样形成函数重载
void print(int &a);
void print(const int &a);
```

```c++
// 声明成员函数为const即指定this指针为const，同样可以构成重载
class LiF {
public:
    void print();
    void print() const;
};
```

函数的形参名不同或返回值类型不同并不构成重载，并且编译报错，因为这类函数被编译器识别为相同函数。

```c++
// 重定义print，编译报错
void print(int a);
void print(int b);
```

```c++
// 重定义print，编译报错
void print(int a);
int print(int a);
```

# 重载是如何实现的

在C++的编译期，编译器会根据参数列表对函数进行重命名，在函数名中按顺序加入参数类型以区分重载的函数。在函数调用时，则会根据实参类型逐个匹配重载的函数，失配则报错。也就是说，重载只是语法上的重载，重载的函数在编译后仍然是不同的函数。这也就解释了为什么只有函数签名不同的函数才能构成重载。在编译完成之后，函数的重载也就确定了。因此可以说，**重载提供的多态性是静态的**，或者说**重载提供了编译期的多态性**。