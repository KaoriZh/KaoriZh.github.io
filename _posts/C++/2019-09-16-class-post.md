---
layout: post
title:  "C++：class"
date:   2019-09-16
excerpt: ""
tag:
- C++
- OOP
---

# class

类是C++的一个重要概念，也是面向对象的一个重要内容。类的行为类似结构体，但功能比结构体的更强大。类是定义该类对象的一个模板，它告诉我们，一个类应该具有什么内容。

## 声明、定义

类用关键字`class`声明、定义。

```c++
class LiF1; // 声明一个类

class LiF2 {}; // 声明并定义一个类
```

如果仅作声明，在声明之后定义之前，它是一个**不完全类型**，这时只能定义指向该类型的指针或引用，可以声明但不能定义以该类型作为参数或返回值的函数。在类的名字出现之后，他就被认为是声明过的，因此，**可以在类内定义指向自身类型的指针或引用**。有时会遇到一些比较复杂的情形，就需要我们先声明一个类，随后再去完善定义。比如：我们需要一个类X包含一个指向类Y的指针，同时，类Y又包含一个类型为X的对象，实现如下：

```c++
class X;

class Y {
X* x;
};

class X {
Y y;
};
```

## 成员

类内的数据称为类的**成员（member）**，成员包括**成员变量**和**成员函数**。成员变量定义类的**属性**，成员函数定义类的**行为**。

## 成员变量

在类内声明的变量称为成员变量。成员变量可以是任何数据类型，甚至可以是指向自身类型的指针或引用。

## 成员函数

声明在类内部的函数称为**成员函数**。成员函数的本质也是普通函数，只是编译器帮我们在成员函数的形参列表首部隐式加上了一个指向该类型的指针，即要求该类的对象才能调用。而在调用的时候，编译器也为我们在实参列表加入了`this`指针，即指向函数调用对象本身的指针。

```c++
class LiF {
public:
    void doNothing(){}
    // 实际的定义是：void doNothing(LiF *this){}
};

LiF lif;
lif.doNothing();
// 实际的调用是：doNothing(this);
```

## 访问权限

在类内，我们可以通过**访问权限限定符**`public`、`protected`、`private`限制类内成员的访问权限。同一个访问限定符可以出现多次，默认状态下，在第一个访问限定符出现之前，类内的访问权限是`private`，在最后一个访问限定符出现之后，所有成员的访问权限都是限定符指定的权限等级。

```c++
class LiF {
    int a; // a是默认的private权限
public:
    int b(); // b()在public限定符下，所以是public权限
protected:
    int c; // c在protected限定符下，所以是protected权限
private:
    int d; // d在private限定符下，所以是private权限
public: // 允许同一限定符重复出现
    int e;
};
```

对应的权限如下：

- `public`：可以被类的成员函数、友元、子类的成员函数和友元、类的对象访问；可以被子类以任何方式继承。
- `protected`：可以被类的成员函数、友元函数、子类的成员函数和友元访问，类的对象无权访问；可以被子类以任何方式继承。
- `private`：只能被类的成员函数、友元访问；无法通过任何方式继承。

与java、C#不同的是，C++限定符的**限制对象是类**，而非类的对象；C++的类也没有访问权限之分。

```c++
class LiF {
public:
	static int sum(const LiF& l1, const LiF& l2) {
		return l1.lif + l2.lif; // 可以直接访问对象的私有成员
	}
private:
	int lif;
};

LiF l1, l2;
LiF::sum(l1, l2);
```

## 内存占用

为了更高效地利用内存，C++采用数据和方法分离的存储方法。即在我们定义类时，程序会为这个类划分一个区域，专门存放这个类的成员函数；而在我们每次定义该类的对象时，程序会按照类的定义为每一个对象都分配一段内存，存储对象数据。也正因此，C++隐式使用对象指针访问成员函数，也出现了`this`指针。

对象占用的内存大小由成员变量占用的内存决定。特殊地，如果在类内包含了其他类的对象，那么内存占用会计入被包含对象的大小；如果只是包含其他类的指针，那么就只增加一个指针的占用。如果在类内声明了虚函数，那么需要额外增加4个字节，用于存储**虚函数表指针**。

```c++
class LiF1 {};
sizeof(LiF1);  // 结果为1，这是因为这个类没有包含任何数据成员，所以仅仅分配1个字节用于标记类

class LiF2 {
public:
	void doNothing(){}
private:
	int lif;
};
sizeof(LiF2); // 结果为4，即sizeof(int)
    
class LiF3 {
public:
    virtual void doSomething() = 0;
};
sizeof(LiF3); // 结果为4，这时有虚函数表指针作为类的标记，所以不需要额外的1个字节

class LiF4 {
public:
    virtual void doSomething() = 0;
private:
    int lif;
};
sizeof(LiF4); // 结果为8，即int的大小+虚函数表指针的大小

class LiF5 {
private:
    int a, b;
};

class LiF6 {
private:
    LiF5 lif5;
};
sizeof(LiF6); // 结果为8，即sizeof(LiF5)

class LiF7 {
private:
    LiF5 *lif5;
};
sizeof(LiF7); // 结果为4，即sizeof(LiF5*)
```

## 内存对齐

为了提高存取效率，类的成员变量都是按4字节对齐的。不满4字节的可以合并，超过4字节的只能另起4字节。

```c++
class l1 {
	char a;
	char b;
	int c;
};

class l2 {
	char a;
	int c;
	char b;
};

sizeof(l1); // 结果为8
sizeof(l2); // 结果为12
```

