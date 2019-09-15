---
layout: post
title:  "C++：inline"
date:   2019-09-15
excerpt: ""
tag:
- C++
- OOP
---

# inline

`inline`是C++提供的一个关键字，它用于函数定义之前，表示把函数定义为**内联函数**。内联函数的含义是：在函数调用点把函数体直接展开，取代函数调用。

```c++
inline int getZero() {
    return 0;
}
int a = getZero();
```

如果上述函数没有定义为`inline`，那么在声明`a`时，程序实际上的工作是，把`getZero()`函数压入栈中，执行函数得到返回值0并出栈，最后把0赋值给`a`。在定义为`inline`之后，编译器会把作为内联函数的函数体在调用点，也就是在声明`a`时展开，直接得到结果0并赋值给`a`，省去了函数入栈和出栈的调用过程，提升了性能。

那么如果是复杂一点的内联函数呢？

```c++
inline int get(int a, int b, int c, int x) {
    return (a * x + b) * x +c;
}
int b = get(1, 2, 1, 4);
```

比起之前的`getZero()`，`get()`函数多了许多运算，它在调用点也会被展开为`int b = (1 * 4 + 2) * 4 + 1;`。可想而知，越复杂的函数体内联展开越困难。

由此也引出了内联函数定义的特点：

- `inline`关键字对编译器起**建议**作用，是否内联编译由编译器决定。
- `inline`关键字出现在**函数声明**处不起作用，出现在**函数定义**时有效。
- 把**逻辑复杂**的函数定义为内联是无意义的，如嵌套调用、递归等。
- 类内定义的函数都是**隐式内联**的，类外定义需要显式加上`inline`关键字。

```c++
// inline无意义
inline int f1(int x) {
    if (x == 0 || x== 1) return 1;
    return f(x - 1) + f(x - 2);
}

// inline无意义
inline int f2();

class LiF {
public:
    LiF() = default;
    LiF(int _lif);
    void set(int _lif) { lif = _lif; } // 类内隐式内联
    int get();
private:
    int lif;
}

LiF::LiF(int _lif): lif(_lif) {} // 无内联

inline int LiF::get() { return lif; } // 类外显式内联
```

既然内联可以提高效率，那为什么不把所有函数都定义为内联呢？这个问题也不难解答。内联的前提是可以在调用点展开，显然之前提到的复杂函数时无法展开的；而如果某次内联函数执行消耗的时间远长于调用的消耗，那么这次内联也是失败的。再者，程序效率提升的背后必然有更大的内存消耗，内联带来的效率提升是通过复制代码到调用点实现的，这显然就增加了代码量。那么这就要求程序员谨慎地使用`inline`。