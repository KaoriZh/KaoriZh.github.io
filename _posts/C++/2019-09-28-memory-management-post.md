---
layout: post
title:  "C++：Memory Management"
date:   2019-09-28
excerpt: "temp"
tag:
- C++
- OOP
---

# 浅谈C++内存管理

## new和delete

在C++中，我们习惯用`new`申请堆中的内存，配套地，使用`delete`释放内存。

```c++
class LiF;
LiF* lif = new LiF(); // 分配内存给一个LiF对象
delete lif; // 释放资源
lif = nullptr; // 指针置空，保证安全
```

与C的`malloc`相比，我们发现，`new`操作在申请内存的同时还完成了对象的构造，这也是`new`运算符做的一层封装。

## 内存是怎样申请的

从`new`这个例子可以看出，C++的内存管理大有门道，而内存管理也是C++中最为重要的一部分。在硬件层之上的第一层封装就是操作系统，高级语言编写的程序也将作为进程在这里接受进程调度，其中就涉及到内存的分配。从这个意义上理解，可以说，内存是向操作系统申请的（不严格正确）。

在C++应用层（Application），我们最常用的是C++ primitive（原语）操作，`new`、`new[]`、`new()`、`::operator new()`等，申请内存。在primitive之上，C++的Library还为我们提供了各种各样的allocator（容器，或者说分配器），如`std::allocator`，可以通过这些容器分配内存，但其实容器还是通过`new`和`delete`运算符去实现内存的申请与释放。在`new`之下，则是Microsoft的CRT库提供的`malloc`和`free`，`new`操作是对malloc的封装。再往下就是操作系统的API。这些内存管理的API的关系大致如下：

![memory-management-1](https://s2.ax1x.com/2019/09/27/uK7xe0.png)

## 再谈new和delete

### new expression

通常，我们会使用`new`在堆中申请一块内存，并把这块内存的地址保存到一个指针，这个操作就是new操作，但严格来说，它其实应该称为**new expression（new表达式）**。

```c++
LiF* lif = new LiF(); // new expression
```

但其实，`new`是一个复合操作，通常会被编译器转换为类似如下的形式：

```c++
LiF* lif;
try {
    void* mem = operator new(sizeof(LiF)); // apply for memory
    lif = static_cast<LiF*>(mem); // static type conversion
    lif->LiF::LiF(); // constructor
} catch(std::bad_alloc) {
    // exception handling
}
```

#### new做了什么

1. 调用`operator new`申请足够存放对象大小的内存；
2. 把申请到的内存交给我们的指针；
3. 最后调用构造函数构造对象。

#### operator new

在`try/catch`块的第一句，new expression调用了`operator new`，它的原型是：

```c++
// 位于<vcruntime_new.h>
_Ret_notnull_ _Post_writable_byte_size_(_Size)
_NODISCARD _VCRT_ALLOCATOR void* __CRTDECL operator new(
    size_t _Size
);

_Ret_maybenull_ _Success_(return != NULL) _Post_writable_byte_size_(_Size)
_NODISCARD _VCRT_ALLOCATOR void* __CRTDECL operator new(
    size_t _Size,
    std::nothrow_t const&
) noexcept;
```

而在`operator new()`会去调用`::operator new()`，最后，`::operator new()`的内部实际上是调用了`malloc`。`operator new()`的工作就是通过`malloc`不断申请内存，直到申请成功。在operator new的第二个重载中可以看到，这是一个`noexcept`的函数，因为我们可以认为，内存的申请总是可以成功的，因为在`operator new()`内部，每当申请失败时，他都会调用一次**new handler**，可以把new handler理解为一个内存管理策略，它会释放掉一些不需要的内存，以便当前的`malloc`可以申请到内存。可以说，**operator new的工作就是申请内存。**

#### placement new

在new拆解得到的第三步，它调用了对象的构造函数，而且在表达上比较特殊：`lif->LiF::LiF();`。编译器通过对象指针直接调用了对象的构造函数，但如果我们在程序中这样写，编译一般是无法通过的，这不是源代码的语法。在上面的语句中，我们已经完成了内存的分配工作，显然这一步是在进行对象的构造，这个操作也被称为**placement new**，即定点构造，在指定的内存块中构造对象。

**new expression是operator new和placement new的复合。**

### delete expression

在我们不再需要某一个对象时，通常使用`delete`析构该对象。delete操作严格来说，与`new`对应，它应该称为**delete expression（delete表达式）**。

```c++
delete lif; // delete expression
lif = nullptr;
```

同样，`delete`也是一个复合操作，通常会被编译器转换为类似如下的形式：

```c++
lif->~LiF(); // destructor
operator delete(lif); // free the memory
```

#### delete做了什么

1. 调用对象的析构函数；
2. 释放内存。

#### operator delete

在delete操作的第二步，实际上是执行了`operator delete()`，它的原型是：

```c++
void __CRTDECL operator delete(
    void*  _Block,
    size_t _Size
) noexcept;
```

而`operator delete`其实是调用了全局的`::operator delete()`，`::operator delete()`又调用了`free`进行内存的释放。

也就是说，**`new`和`delete`是对`malloc`和`free`的一层封装**，这也对应了上面图中的内容。

### array new和array delete

**array new**即`new[]`，顾名思义，它用于构造一个对象数组。

```c++
class LiF {
public:
	LiF(int _lif = 0): lif(_lif) {}
	int lif;
};

LiF* lifs = new LiF[3]; // right
LiF* lifs = new LiF[3](); // right
LiF* lifs = new LiF[3](1); // wrong, no param accepted
LiF* lifs = new LiF[3]{1}; // right, but only lifs[0].lif equals 1
```

`array new`的工作是申请一块足以容纳指定个数的对象的内存（在本例中是3个LiF对象）。在前两种写法中，array new调用的是默认构造函数，这种情况下只能默认构造对象，但如果又想要给对象赋予非默认的初值，那么就需要使用到placement new了。

```c++
LiF* lifs = new LiF[3];
LiF* p = lifs;
for (int i = 0; i < 3; ++i) {
	new(p++)LiF(i+1); // placement new
	cout << lifs[i].lif << endl;
}
```

直观地，placement new并不会分配内存，它只是在已分配的内存上构造对象。对应地，使用array new构造的对象需要使用array delete释放内存。

```c++
delete[] lifs;
```

相较于array new，array delete不需要提供数组长度参数。这是因为，在使用array new构造对象的时候，还有一块额外的空间用于存放**cookie**，也就是这块内存的一些信息，其中就包括这个内存块的大小和对象的数量等等。

```c++
class LiF {
public:
    //...
    ~LiF() { cout << "des" << endl; }
};

delete[] lifs; // array delete
```

此时我们显式地定义析构函数，并且在析构函数被调用时打印信息。在运行到`delete[]`的时候，程序就会根据cookie中的信息，准确地释放对应的内存块，本例中，“des”会被打印三次，即3个对象的析构函数都被调用了。此时如果错误地调用delete而非array delete，那么就可能会发生内存泄漏。

```c++
delete lifs; // delete
```

这时只会调用一次析构函数，但本例中并不会发生泄漏，这个简单的类中并没有包含其他对象。再看下面这种情况：

```c++
class LiF2 {
public:
	LiF2() : lif(new LiF()) {}
	LiF2(const LiF& _lif) : lif(new LiF(_lif.lif)) {}
	~LiF2() { delete lif; lif = nullptr; }
private:
	LiF* lif;
};

LiF2* lif2 = new LiF2[3];
delete lif2; // call "delete" by mistake
```

这时，由于错误地使用了delete，析构函数只会被调用一次，也就是说，还有另外两个对象，虽然对象本身被销毁了，但对象中的`lif`指针所指的对象却没有被销毁，即：**对象本身不会发生泄漏，泄漏的是对象中指针保存的内存**。

## 深入placement new

之前提到的`new()`操作以及new expression拆解的第三步，其实都是placement new。在主动使用placement new时，它的一般格式为：

```c++
new(pointer)Constructor(params);
// or
::operator new(size_t, void*);
```

它的作用是：把对象（object）构造在已分配的内存（allocated memory）中。同样也可以在`vcruntime_new.h`中找到相关定义：

```c++
#ifndef __PLACEMENT_NEW_INLINE
    #define __PLACEMENT_NEW_INLINE
    _Ret_notnull_ _Post_writable_byte_size_(_Size) _Post_satisfies_(return == _Where)
    _NODISCARD inline void* __CRTDECL operator new(size_t _Size, _Writable_bytes_(_Size) void* _Where) noexcept
    {
        (void)_Size;
        return _Where;
    }

    inline void __CRTDECL operator delete(void*, void*) noexcept
    {
        return;
    }
#endif
```

可以看到，placement new并没有做任何工作，它只是把我们传递的指针又`return`了回来。结合下面的例子就不难理解这个逻辑。

```c++
class LiF {
public:
	LiF(int _lif = 0): lif(_lif) {}
	int lif;
};

LiF* lifs = new LiF[3]; // array new
LiF* lif = new(lifs)LiF(); // placement new
```

我们在array new得到的`LiF`对象数组中的第一个对象上使用了placement new，同样拆解这个new操作可以得到类似上面普通`new`的一个`try/catch`块：

```c++
LiF* lif;
try {
    void* mem = operator new(sizeof(LiF), lifs); // placement new
    lif = static_cast<LiF*>(mem); // static type conversion
    lif->LiF::LiF(); // constructor
} catch(std::bad_alloc) {
    // exception handling
}
```

此外，在`__PLACEMENT_NEW_INLINE`宏还包含了一个placement delete的定义：

```c++
inline void __CRTDECL operator delete(void*, void*) noexcept
{
	return;
}
```

可以看到，它也是不做任何工作的，所谓的placement delete只是为了形式上的统一。

# 总结

- 内存的申请释放可以在不同层面上进行，但只要是在操作系统之上，都是基于malloc/free。
- 在C++ primitive层，通常使用new和delete系列，new是对malloc的封装，delete是对free的封装。
- 通常new是指new expression。严格来说，new的含义有三种：new expression、operator new和placement new。new expression是operator new和placement new的复合，operator new负责内存的申请，placement new负责对象的构造；此外还有new[]。
- 所有的内存申请/释放操作都必须配套使用。