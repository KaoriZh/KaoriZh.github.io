---
layout: post
title:  "C++：Copy & Reference Count"
date:   2019-09-19
excerpt: ""
tag:
- C++
- OOP
---

# 浅拷贝、深拷贝

通常，我们会按如下方式书写拷贝构造函数：

```c++
class LiF {
public:
	LiF(int _lif = 0) : lif(_lif) {} // 默认构造函数
	LiF(const LiF& l) : lif(l.lif) {} // 拷贝构造函数
private:
	int lif;
};
```

这是正确的。但是，如果数据成员包含指针类型的话，这种写法就很危险了。

```c++
class LiF {
public:
	LiF() { lif = new int(0); } // 为lif动态分配内存
	LiF(const LiF& l) : lif(l.lif) {} // 拷贝构造函数
	~LiF() { // 析构函数
        delete lif; // 释放分配给lif的资源
        lif = nullptr; // 置空
    }
private:
	int* lif;
};

LiF l1;
LiF l2(l1); // 程序结束析构l2时，程序将崩溃
```

在拷贝`l1`生成`l2`的时候，我们的构造函数只是简单的把`l1`的`lif`成员的值赋予了`l2`的`lif`，也就是说，它们保存的都是`l1`构造时分配的地址，当两者之中的某个对象被销毁时，构造函数正常执行，资源被释放，但之后如果另一个对象也被析构，`lif`的资源就会被重复释放，`lif`也就变成野指针。这种拷贝方式也称为**浅拷贝**，即只拷贝空间，不拷贝资源。

为了防止指针类型的数据成员出现野指针错误，对应地便有了**深拷贝**操作，即在拷贝对象内容的同时为拷贝的内容分配新的资源。

```c++
class LiF {
public:
	LiF() { lif = new int(0); } // 为lif动态分配内存
	LiF(const LiF& l) : lif(new int(*l.lif)) {} // 深拷贝构造函数
	~LiF() { // 析构函数
        delete lif; // 释放分配给lif的资源
        lif = nullptr; // 置空
    }
private:
	int* lif;
};

LiF l1;
LiF l2(l1);
```

注意到，在上面的拷贝构造函数中，我们为新对象的`lif`成员分配了一块新的内存，即完成了深拷贝。

# 类的行为

从上面的例子可以得到两种抽象的类行为：**行为像值**、**行为像指针**。

## 行为像值的类

即类提供的构造函数是深拷贝，类的每个对象都有自己的一份拷贝。对于这样的类，它显然需要：一个深拷贝构造函数、一个深拷贝赋值运算符重载、一个可以释放成员占用的资源的析构函数。

```c++
class LiF {
public:
	LiF(const int& _lif = 0): lif(new int(_lif)) {}
	LiF(const LiF& l) : lif(new int(*l.lif)) {}
	LiF& operator= (const LiF& l) {
		lif = new int(*l.lif);
		return *this;
	}
	~LiF() {
        delete lif;
        lif = nullptr;
    }
private:
	int* lif;
};
```

## 行为像指针的类

即类提供的是浅拷贝，但由于可能有多个对象成员值相同一段内存，所以我们不能在析构时简单地释放资源。为了解决浅拷贝带来的野指针问题，需要引入一种技术——**引用计数（reference count）**。这也是C++11的智能指针**shared_ptr**的实现。

**引用计数：**

- 在每个构造函数初始化对象时，额外创建一个引用计数并置为1，用以记录有多少对象正在共享资源。
- 在执行拷贝操作时进行浅拷贝，同时拷贝计数器，并递增计数器，指出共享的对象增加了一个。
- 在进行拷贝赋值时比较特殊但也很容易理解：需要递增右侧对象的计数器并递减左侧对象的计数器。若左侧对象引用计数归零，则释放资源。
- 在析构对象时，并不直接释放共享的资源，而是递减计数器，直至计数器归零才释放资源。

```c++
class LiF {
public:
	LiF(const int& _lif = 0): lif(new int(_lif)), referenceCount(new unsigned(1)) {}
	
	LiF(const LiF& l) : lif(new int(*l.lif)) {}
	
	LiF& operator= (const LiF& l) {
		++ *l.referenceCount;
		if (-- *referenceCount == 0) {
			delete lif;
			delete referenceCount;
		}
		lif = l.lif;
		referenceCount = l.referenceCount;
		return *this;
	}
	
	~LiF() {
		if (-- *referenceCount == 0) {
			delete lif;
			delete referenceCount;
		}
	}
private:
	int *lif;
	unsigned *referenceCount;
};
```

