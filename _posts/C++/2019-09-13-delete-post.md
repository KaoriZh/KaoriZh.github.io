---
layout: post
title:  "C++：= delete"
date:   2019-09-13
excerpt: "temp"
tag:
- C++
- OOP
---

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

再看下面这个场景：

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