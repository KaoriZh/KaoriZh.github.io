---
layout: post
title:  "C++：= default & = delete"
date:   2019-09-13
excerpt: "temp"
tag:
- C++
- OOP
---

# = default

## default的由来

C++有一类特殊的成员函数：构造函数、析构函数、赋值运算符重载函数，负责类的构造、初始化、拷贝赋值、销毁。在调用类的特殊成员函数，而类内没有声明该函数时，编译器会为我们自动生成一个默认的函数，如：

```c++
class LiF {
private:
    int lif;
};

LiF lif;
```

在实例化一个类的对象时，一定会调用这个类的某个构造函数，在上面的例子中，没有定义构造函数但也能通过编译，是因为我们没有定义任何构造函数，编译器会为我们自动生成一个默认构造函数，这个构造函数不做任何工作，相当于：

```c++
class LiF {
public:
	LiF();
private:
    int lif;
};

LiF::LiF() {}

LiF lif;
```

但只要在定义类时声明了非默认构造函数，编译器就不会为我们生成默认构造函数，在下面的例子中，编译将无法通过：

```c++
class LiF {
public:
	LiF(int _lif);
private:
    int lif;
};

LiF::LiF(int _lif) {
    lif = _lif;
}

LiF lif;
```

这时就需要额外定义一个默认构造函数。类似地，其他特殊成员函数定义了非默认函数的情况下，编译器也不会生成对应的默认函数。这时就需要额外定义一个默认函数，这显然加大了工作量；此外，手动定义的默认函数效率比编译器自动生成的要低。

由此，**C++11**引入了`default`关键字，显式地让编译器生成一个默认函数：

```c++
class LiF {
public:
	LiF() = default;
	LiF(int _lif);
private:
    int lif;
};

LiF::LiF(int _lif) {
    lif = _lif;
}

LiF lif;
```

也可以把`default`移到类外：

```c++
class LiF {
public:
	LiF();
	LiF(int _lif);
private:
    int lif;
};

LiF::LiF() = default;

LiF::LiF(int _lif) {
    lif = _lif;
}

LiF lif;
```

## default的好处

引入`default`之后，在减轻了程序员的工作量的同时又获取了编译器自动生成的默认特殊成员函数的效率。

## default示例

需要注意的是，`default`关键字只能用于无参的特殊成员函数之后。

```c++
#include <iostream>
using namespace std;

class LiF {
public:
    LiF() = default;
    LiF(int _lif) {
        lif = _lif;
    }
    LiF(const LiF& l) = default;
    LiF& operator= (const LiF& l) = default;

    void print(){cout << lif << endl;}

    ~LiF() = default;
private:
    int lif;
};

int main() {
    LiF* lif1 = new LiF{2};
    LiF lif2 = *lif1;
    lif2.print();
    delete lif1;
    lif1 = nullptr;
    return 0;
}

// 输出结果为 2
```

# = delete

## delete的由来

如之前提到的，在没有声明默认特殊成员函数的时候，编译器会自动帮我们补充，但有时候我们并不希望存在这些函数，比如：我们不希望某个类通过拷贝的方式实例化一个新的对象。就算我们不定义拷贝构造函数和重载拷贝赋值运算符，编译器也会为我们自动完成。

```c++
#include <bits/stdc++.h>
using namespace std;

class LiF {
public:
    LiF() = default;
    LiF(int _lif) {
        lif = _lif;
    }
    void print(){cout << lif << endl;}
    ~LiF() = default;
private:
    int lif;
};

int main() {
    LiF* lif1 = new LiF{2};
    LiF lif2 = *lif1; // 我们不希望这个类可以拷贝构造
    lif2.print();
    delete lif1;
    lif1 = nullptr;
    return 0;
}
```

写出如上代码之后，编译可以通过，但违反了我们的初衷。

在看下面这个场景：

```c++
double add(double x, double y) {
    return x + y;
}

int main() {
    int a, b;
    add(a, b);
    return 0;
}
```

这里我们声明了`double`类型的`add`函数，编译可以通过。假设此时我们不希望其他类型能通过隐式转换调用这个函数，那这里就需要禁用掉会发生转换的版本。

由此**C++11**引入了`delete`关键字，用于显式禁用某些函数。

## delete示例

与`default`不同的是，`delete`没有限制函数必须是类的特殊成员函数。

```c++
#include <bits/stdc++.h>
using namespace std;

class LiF {
public:
    LiF() = default;
    LiF(int _lif) {
        lif = _lif;
    }
    LiF(const LiF& l) = delete; // 显式禁用拷贝构造函数
    LiF& operator= (const LiF& l) = delete; // 显式禁用拷贝赋值运算符

    void print(){cout << lif << endl;}

    ~LiF() = default;
private:
    int lif;
};

int main() {
    LiF* lif1 = new LiF{2};
    LiF lif2 = *lif1; // 这里将引发报错，因为拷贝赋值运算符已被显式禁用
    lif2.print();
    delete lif1;
    lif1 = nullptr;
    return 0;
}
```

```c++
double add(double x, double y) {
    return x + y;
}
int add(int, int) = delete; // 显式禁用add函数的int版本

int main() {
    int a, b;
    add(a, b); // 这里将引发报错，因为对应的函数已被显式禁用
    return 0;
}
```

这里有一点需要注意的是，`delete`关键字仅仅禁用了函数的调用，但在编译过程中，名字查找和重载解析时，该函数名仍是一个有效的标识符。

分析第二个例子：我们声明并定义了`double`类型的`add`函数，声明并显式禁用了`add`函数的`int`重载。在编译到`add(a, b)`时，编译器进行名字查找，找到全局作用域的`add`函数定义，并通过精确匹配最终定位到`int add(int, int)`这个重载上，随后编译器发现这是一个deleted（已被禁用）函数，引发报错。