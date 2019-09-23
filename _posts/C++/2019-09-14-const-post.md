---
layout: post
title:  "C++：const"
date:   2019-09-14
excerpt: ""
tag:
- C++
- OOP
---

# const

`const`是C++提供的一个强大的关键字，`const`的用法非常多，但总的来说，`const`的作用只有一个：保证被修饰的内容不会被程序所修改。

## const基本用法

对一个类型的对象使用`const`修饰即限定这个对象是只读的，不能进行修改操作，由于无法进行修改操作，这也就要求我们在声明`const`对象时必须同时赋值或初始化。const对象的初始化一般是如下形式：

```c++
const TypeName Var = Expression;
```

示例：

```c++
const int a = 0;
int const a = 0; // 等价写法
a = 1; // 编译报错
```

可以注意到的是，const对象的初始化是用表达式初始化的，只是在我们的示例中使用的是常量表达式。事实上，const初始化可以是如下形式：

```c++
int getA() {
    return 0;
}
const int a = 0; // 字面量0是常量表达式，在编译期就能确定，a在编译期完成初始化
const int b = getA(); // getA()在编译期编译，b在运行时初始化
```

一个特殊情况是，期望这个`const`对象存在于全局作用域，那么可以在声明时加上`extern`修饰，那么就可以在声明时不赋初值，但必须确保程序至少有一处声明赋初值。

```c++
// A.cpp
extern const int a;

// B.cpp
extern const int a = 0;
```

## const引用

const引用是对const对象的引用，也就是说，它只是确保不去修改引用的内容，与引用的对象是否是`const`对象无关。

```c++
int a = 0;
const int b = 0;
const int &c = a; // 正确，可以直接修改a的值，但不能通过c修改a
const int &d = b; // 正确，b、d均不可修改
```

需要注意的是，把const引用绑定到临时对象上是非法的。

```c++
int a = 0;
const int &b = a + 1; // 这里a+1生成了一个临时对象
//等价于
const int temp1 = a + 1;
const int &b = temp1;

const double &c = a; // 这里通过隐式类型转换生成了临时对象
// 等价于
const double temp = a;
const double &c = temp;
```

虽然可以通过编译，但这是无意义的引用绑定。

## const与指针

const也可以修饰指针。由于多级指针的存在，const的结合也就变得复杂起来。

```c++
int a = 0;
const int b = 0;

const int *c = &a; // 合法，c是一个指向常量的指针，尽管a本身不是常量
// 等价于
int const *c = &a;

const int *d = &b; // 合法，b是常量，c是指向常量的指针
int *e = &b; // 非法，b是常量，普通指针无法指向常量地址
const int *f = &b; // 合法，b是常量，f是指向常量的指针
```

最常见的是**常量指针**和**指向常量的指针**，前者表示指针是一个常量，即指向的地址不能修改，后者表示指向的地址所存储的内容是常量。常量指针和指向常量的指针是用`const`修饰的一级指针的两种情况，在《C++ Primer》一书中，两者分别称为**顶层const**和**底层const**。

```c++
const int a = 0;
const int *b = &a; // b是指向常量的指针，底层const
int c = 0;
int * const d = &c; // d是一个常量指针，顶层const
const int * const e = &a; // e是一个指向常量的常量指针
```

在涉及到多级指针时，可以从右往左阅读声明表达式，确认`const`修饰的是哪一级。

```c++
int a = 0;
int *b = &a; // b是一个一级指针
int **c = &b; // c是一个二级指针
int **const *d = &c; // d是一个三级指针
/* 
从右往左阅读表达式：
1.首先声明了一个变量d
2.下一个是*，说明d是一个指针，它指向了一个对象
3.接着是const，说明它指向的这个对象不能修改
4.接着又是一个*，说明指向的对象也是一个指针
5.然后是最后的*，说明指向的指针指向的对象仍是一个指针
6.最后是int，说明最后一级指针指向的是一个int类型的地址
在理解这个声明之后很容易就可以对下面的赋值做判断
*/
d = &c; // 正确，d是一个普通指针
*d = &b; // 错误，解引用d得到的是一个常量对象
**d = &a; // 正确，二次解引用d得到的是一个普通指针
```

## constexpr表达式

前面提到，可以用常量表达式或者非常量表达式初始化const对象。所谓的常量表达式是指在编译期就可以得到结果的表达式，由常量表达式初始化的const对象也可以参与组成常量表达式。在某些时候，我们希望一个表达式能在编译期就得到确定，但在复杂的项目中确认一个对象是不是常量表达式非常困难，由此C++引入了`constexpr`关键字，用于显式说明某个对象是常量表达式。

```c++
constexpr int a = 0; // 正确，用字面量0初始化常量表达式
constexpr int b = getB(); // 正确与否取决于getB()是否是常量表达式
```

由于需要在编译期就确定constexpr对象的值，这也就对指针和引用的constexpr初始化提出了更严格的要求：一般情况下，定义在函数内的对象地址无法在编译期确定，因此无法作为初始化常量表达式的值，相反，全局对象可以。

## const与函数

`const`可以修饰函数的形参。

```c++
int LiF(const int lif);
// 正确，在函数内部不能修改lif
// 当然，形参本身只是一个拷贝，在函数调用过程中发生的修改并不会反馈到实参
int LiF(const int *lif);
// 正确，保护原数据不被修改
int LiF(const int &lif);
// 正确，这是最常用的写法，兼具效率与安全性
```

`const`还可以修饰函数的返回值。`const`确保函数的返回值不会被修改，即无法用作左值。

```c++
const int& LiF(int &lif) {
    return lif;
}
```

## const与类

`const`可以修饰类的**成员**，由于调用构造函数时就已经确认了对象的内容，也就是说，const成员需要在构造函数之前初始化，那么，被修饰的类成员只能通过初始化列表初始化。

```c++
class LiF {
public:
    LiF(int _lif): lif(_lif) {}
private:
    const int lif;
};
```

const可以修饰类的**成员函数**，被修饰的成员函数称为常成员函数，常成员函数可以被所有对象调用，但常对象只能调用常成员函数。这是因为，成员函数的参数列表里隐式传递了一个`this`指针，用`const`修饰成员函数，实际上是修饰`this`，而`const *`是没办法转换成普通指针类型的，故不能调用普通成员函数。又由于函数重载不会忽略掉底层const，故根据成员函数的`const`也可以构成重载。非常对象会通过精确匹配找到普通成员函数，而常对象则会匹配到对应的常成员函数。

```c++
class LiF {
public:
    int get() { return lif; }
    int get() const { return lif; } // 常成员函数重载
private:
    int lif;
};

LiF l1;
const LiF l2;
l1.get(); // 调用的是int get();
l2.get(); // 调用的是int get() const;
```

有时我们希望类的成员能记录某些信息，即便是在const对象内。这时就需要一个永远可变的成员，对应地，C++提供了`mutable`关键字。

```c++
class LiF {
public:
    void count() const {
        lif++;
    }
private:
    mutable int lif;
};

LiF l1;
const LiF l2 = l1;
l2.count();
```

`const`还可以修饰成员函数的返回值，与普通函数的const返回值类似，以禁止链式调用，或者说禁止返回值成为左值。

```c++
class LiF {
public:
    const LiF& operator= (const LiF &l) {
        lif = l.lif;
        return *this;
    }
    const LiF& set(int _lif) {
        lif = _lif;
        return *this;
    }
private:
    int lif;
};

LiF l1, l2, l3;
l1 = l2 = l3; // 合法
(l1 = l2) = l3; // 非法，重载后的赋值运算符返回值是常量，不能再次赋值

LiF l4;
l4.set(1); // 合法
l4.set(1).set(2); // 非法，set(1)之后返回的是常量this
```

