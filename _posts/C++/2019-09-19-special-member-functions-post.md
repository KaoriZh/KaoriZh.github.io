---
layout: post
title:  "C++：Special Member Functions"
date:   2019-09-19
excerpt: ""
tag:
- C++
- OOP
---

# Special Member Functions

区别于定义类的行为的普通成员函数，类内有一类特殊的成员函数，它们负责类的**构造**、**拷贝**、**移动**、**销毁**。

## 构造函数

构造函数控制对象的初始化过程，具体来说，就是初始化对象的数据成员。构造函数的名字与类名相同，且没有返回值。构造函数也可以有**重载**，重载区别于参数数量或参数类型。与其他成员函数不同的是，构造函数不能被声明为`const`，对象的常量属性是在构造函数完成初始化之后获得的。

### 默认构造函数

默认构造函数的工作是：如果在类内定义了成员的初始值，那么用初始值初始化成员；否则，默认初始化成员。

> 默认初始化是指定义变量时不赋予初始值时被赋予默认值的动作。定义于函数体外的内置类型如果没有被显式初始化，则赋值0；在函数体内定义的变量不会被初始化。

```c++
class LiF {
public:
    LiF(int _lif = 0) { lif = _lif; } // 指定了lif的初值，这是一个默认构造函数
private:
    int lif;
}

LiF l; // 调用默认构造函数，此时l.lif值为0
```

再看下面这种情况：

```c++
class LiF1 {
public:
	LiF1(int _lif = 0) { lif = _lif; }
	int lif;
};

class LiF2 {
public:
	LiF1 lif1;
};

LiF2 l2;
std::cout << l2.lif1.lif << std::endl; // 输出结果是0
```

在上面的例子中，我们并没有为`LiF2`定义默认构造函数，但它又执行了默认构造。这是因为，**当类没有定义任何构造函数，而程序又需要用到构造函数时，编译器会自动生成一个合成的默认构造函数（synthesized default constructor）**。需要注意的是，只有在类内所有成员都具有类内初始值的时候，编译器才能合成默认构造函数。

```c++
class LiF {
public:
	void print() { cout << lif << endl; }
private:
	int lif;
};

LiF l;
l.print();
```

在上面的代码中，看似是我们需要一个默认构造函数来完成对`l`的初始化，理所应当地，编译器应该为我们生成一个合成的默认构造函数，但实际运行时，发现`l.lif`的值是未定义的。再看下面这种情况：

```c++
class LiF {
public:
	int lif;
};

LiF l;
if (l.lif) {
	cout << l.lif << endl;
}
```

这次连编译都没有通过，报错信息指出程序正在试图访问一个未初始化变量，即`l.lif`。通过上面这两种情况可以看出，编译器并不会因为程序“需要”默认构造函数，就自动生成一个合成的默认构造函数。事实上，只有下面几种情况，编译器会生成合成的默认构造函数：

1. 类含有类对象成员，且该对象类型有默认构造函数（对应第一个例子）。
2. 类继承自带有默认构造函数的类。
3. 类内带有虚函数，由于虚函数表指针的存在，每个对象的构造都需要赋予该指针正确的值，而这个工作由默认构造函数完成。
4. 类虚继承自另一个类，虚继承的派生类包含一个指向虚基类的指针，该指针同样需要正确的值，这个工作同样由默认构造函数完成。

C++11提供了`default`关键字，可以通过指定 `= default`来显式生成默认构造函数。

```c++
class LiF {
public:
	LiF() = default;
	void print() { cout << lif << endl; }
private:
	int lif;
};

LiF l;
l.print(); // l.lif的值是未定义的
```

此外，如果类内包含一个无法默认初始化的`const`成员，那么编译器也会拒绝生成默认构造函数。

```c++
class LiF {
public:
	LiF() = default;
	void print() { cout << lif << endl; }
private:
	const int lif;
};

LiF l; // 编译无法通过，提示默认构造函数被禁用
```

《C++ Primer》也建议，**不要依赖编译器提供的合成的特殊成员函数**。

### 构造函数初始值列表

构造函数还可以包括一部分特殊的内容，这部分称为**构造函数初始值列表（constructor initialize list）**，C++建议，在列表内完成成员的初始化。相比在构造函数体内初始化，初始值列表可以初始化常量成员。

```c++
class LiF {
public:
    LiF(int _lif) { lif = _lif; } // 编译报错，提示常成员lif没有初始化
    LiF(int _lif): lif(_lif) {} // 通过编译
private:
    const int lif;
};
```

当**使用初始值列表**初始化一个对象时，列表参数的顺序并不影响成员的初始化顺序，决定初始化顺序的是成员的定义顺序。良好的编程规范是，**初始化列表的成员顺序尽量与成员的定义顺序保持一致**。

```c++
class LiF {
public:
	LiF(int val): b(val), a(b) {}
private:
	int a;
	int b;
};
```

### 委托构造函数

C++11扩展了构造函数初始值列表的功能，允许定义**委托构造函数（delegating constructor）**。委托构造函数可以通过初始化列表把初始化任务委托给之前已经定义过的构造函数。

```c++
class LiF {
public:
    LiF(int _a, int _b): a(_a), b(_b) {} // 普通构造函数
    LiF(): LiF(0, 0) {} // 通过委托定义了默认构造函数
private:
    int a;
    int b;
};
```

### 转换构造函数

如果一个类存在一个只接受单个参数的构造函数，那么这个函数就定义了一个**从参数类型向类类型隐式转换**的规则，这个函数也被称为**转换构造函数（converting constructor）**。这种隐式转换无法嵌套，即编译器只会自动做一次隐式转换。

```c++
class LiF {
public:
	LiF(int _lif = 0) : lif(_lif) {}
	void doNothing(const LiF &l) {} // doNothing()函数需要一个LiF对象的引用
private:
	int lif;
};

LiF l1;
l1.doNothing(1); // 这里执行了隐式转换，用参数1生成了一个LiF对象
```

隐式转换可能带来一些无法预知的后果，有时我们并不希望隐式转换的发生。C++提供了`explicit`关键字以禁用这种隐式转换。`explicit`只允许出现在函数声明处，且只适用于单参数构造函数，多参数构造函数并不存在隐式转换规则。

```c++
class LiF {
public:
	explicit LiF(int _lif = 0) : lif(_lif) {}
	void doNothing(const LiF &l) {} // doNothing()函数需要一个LiF对象的引用
private:
	int lif;
};

LiF l1;
l1.doNothing(1); // 编译无法通过，因为隐式转换已被禁用
```

### 拷贝构造函数

```c++
class LiF {
public:
    LiF();
    LiF(const LiF& l): lif(l.lif) {}
    int lif;
};
```

在翻阅原码的时候，经常能见到形如上面的类。其中第二个构造函数就是**拷贝构造函数（copy constructor）**。最常见的拷贝构造函数往往是：形参列表只包含一个自身类类型的引用，由于拷贝过程中并不会改变被拷贝的对象，这个引用一般也是按`const`属性传递。在定义一个对象时，如果采用`=`的方式初始化，那么执行的就是拷贝初始化。

```c++
string s1("s"); // 直接初始化
string s2(s1); // 直接初始化
string s3 = s1; // 拷贝初始化
string s4 = "s"; // 隐式拷贝初始化
string s5 = string("s"); // 显式拷贝初始化（等价于s4）
```

为什么是按引用传递呢？**如果按值传递，在传递过程中会隐式调用拷贝构造函数生成函数实参，引发无限循环调用。**

通常情况下，拷贝构造函数都是被隐式调用的，因此一般不声明为`explicit`。和默认构造函数类似，如果程序没有定义拷贝构造函数，在需要时，编译器会自动生成一个**合成的拷贝构造函数（synthesized copy constructor）**，这个函数会拷贝对象的所有成员，但不同的是，即使我们定义了其他（非拷贝）构造函数，编译器也会生成。拷贝构造函数也可以使用初始化列表。需要注意的是，合成的拷贝构造函数进行的是**浅拷贝**。

调用拷贝构造函数的场景：

1. 用`=`定义对象
2. 把对象作为实参传递给非引用类型的形参（这也解释了为什么拷贝构造函数要按引用传递）
3. 返回一个非引用类型的对象
4. 用花括号列表初始化数组元素或聚合类成员

```c++
string a;
void doNothing(string a);
doNothing(a); // 对应2

string doNothing();
doNothing(); // 对应3

string s[2] = {"1", "2"}; // 对应4.1

struct LiF {
    string a;
};
LiF lif = {"a"}; // 对应4.2
```

### 移动构造函数

在C++11中，出现了对象移动的特性。在某些情况下，我们拷贝的对象会被立即销毁，如：使用函数调用的返回值给对象赋值。这种拷贝是不必要的，在这种情况下，更好的方法是**移动（move）对象**。为了支持移动操作，C++11提供了**move语义**以及**右值引用**。`move`被定义在标准库中，用于把一个左值转换为右值引用，所谓右值引用即绑定到临时对象的引用。有了右值和move，就可以轻松定义移动构造函数：

```c++
#include <iostream>

class LiF {
public:
	LiF(int _lif = 0) : lif(_lif) { std::cout << "default" << std::endl; } // 默认构造函数
	LiF(const LiF& l) : lif(l.lif) { std::cout << "copy" << std::endl; } // 拷贝构造函数
	LiF(LiF&& l) : lif(l.lif) { std::cout << "move" << std::endl; } // 移动构造函数
private:
    int lif;
};

int main() {
	LiF l1; // 调用默认构造函数
	LiF l2 = l1; // 调用拷贝构造函数
	LiF l3 = std::move(l1); // 调用移动构造函数
	return 0;
}
```

当一个类同时存在拷贝和移动构造时，编译器会通过参数是否是右值判断应该使用哪种构造函数。当参数是右值时，编译器会选择移动构造，在移动构造函数中，表面上是把右值引用的对象赋值给待构造的对象，实际上，资源的所有权已经发生了改变，待构造的对象“窃取”了资源。

## 运算符重载

### 拷贝赋值运算符重载

通过重载赋值运算符`=`，类也可以控制对象的赋值。类似地，如果一个类没有定义拷贝赋值运算符，编译器会生成一个**合成拷贝赋值运算符（synthesized copy-assignment operator）**。回顾一般的赋值操作：首先给`=`左侧的对象赋予右侧对象的值，然后返回整个表达式的值（左侧对象）。为了与一般的赋值操作对应，在重载赋值运算符时，通常把返回类型置为对象引用并返回左侧对象（`*this`）。同样地，合成的拷贝赋值运算符进行的也是**浅拷贝**。

```c++
class LiF {
public:
	LiF(int *_lif): lif(_lif) {}
    LiF(const LiF &l): lif(l.lif) {} // 显式定义拷贝构造函数
	LiF& operator= (const LiF& l) {
		lif = l.lif;
		return *this;
	} // 显式定义拷贝赋值运算符重载
	int *lif;
};

int a;
LiF l1(&a);
LiF l2 = l1; // 此时l1、l2中的lif成员指向的都是a的地址
```

拷贝构造函数与拷贝赋值运算符的行为很相似，但通过他们的名字可以很好地理解它们的工作：**拷贝构造函数负责拷贝一个对象并构造另一个对象，拷贝赋值运算符负责拷贝一个对象的内容并赋值给一个已存在的对象，即两者都区别为由于新对象生成。**

### 移动赋值运算符重载

类似地，我们还可以重载`=`进行移动赋值。

```c++
#include <iostream>
using std::string;
using std::cin;
using std::cout;
using std::endl;

class LiF {
public:
	LiF(string _lif = "lif") : lif(_lif) { cout << "default" << endl; } // 默认构造函数
	LiF(const LiF& l) : lif(l.lif) { cout << "copy" << endl; } // 拷贝构造函数
	LiF(LiF&& l) noexcept : lif(l.lif) { cout << "move" << endl; } // 移动构造函数
	LiF& operator= (const LiF &l) { // 拷贝赋值
		cout << "copy=" << endl;
		lif = l.lif;
		return *this;
	}
	LiF& operator= (LiF &&l) noexcept { // 移动赋值
		cout << "move=" << endl;
		if (this != &l) {
			std::swap(lif, l.lif);
		}
		return *this;
	}
	~LiF() { cout << "destruct" << endl; }
	void print() { cout << lif << endl; }
private:
	string lif;
};

int main() {
	LiF l1; // 调用默认构造函数
	LiF l2 = l1; // 调用拷贝构造函数
	LiF l3 = std::move(l1); // 调用移动构造函数
	return 0;
}
```

同样，移动赋值运算符接受的参数也是一个右值引用。编译器也会在需要时合成移动构造函数和移动赋值运算符。但只有当一个类没有定义任何拷贝控制，且其所有成员都是可移动构造或移动赋值时，编译器才能合成。

## 析构函数

最后一类特殊成员函数叫做**析构函数（destructor）**，与构造函数相反，析构函数负责释放对象占用的资源，销毁对象的数据成员。同样地，当一个类没有定义自己的析构函数时，编译器会生成一个**合成析构函数（synthesized destructor）**。

```c++
class LiF {
public:
	~LiF(){}
};
```

析构函数的名字为`~`加上类名，且不接受任何参数。每个类类型的对象被销毁都会执行自己的析构函数，内置类型没有析构函数。与构造函数不同的是，析构函数的析构部分是隐式的，并不会出现在函数体内。析构函数的函数体用于进行一些额外的操作，如：销毁相关对象，打印debug信息等。**真正的析构发生在析构函数函数体之后**，且成员按初始化顺序的**逆序**销毁。特殊地，析构函数不能被声明为`delete`。

## 总结

在编写一个类时，无论是否需要，都应该显式地定义以上特殊成员函数（移动构造和移动赋值可以视情况而定）。