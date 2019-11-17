---
layout: post
title:  "C++：Variadic Templates"
date:   2019-10-14
excerpt: "temp"
tag:
- C++
- OOP
---

# Variadic Templates

Variadic Templates即可变参数模板，是C++11提供的新功能，对应的关键字是`...`。**可变参数的含义是：模板的参数个数是可变的，且可变的参数的类型可以是各不相同的。**其实对于可变这一说法，早在C中就有了，我们熟知的`printf`函数接受的参数个数其实就是任意的。

```c
printf("%d %c %s", d, c, s);
```

可变参数这一特性和模板结合，形成了更为强大的功能。看一个简单的例子：

```c++
void print() {}
template<typename T, typename... Ts>
void print(const T& firstArg, const Ts&... otherArgs) {
    std::cout << firstArg << std::endl;
    print(otherArgs...);
}
```

这里定义了一个函数模板，它接受两个参数，第一个是`T`类型的参数，第二个比较特殊，它接受的是一组参数。而这个函数的工作也是比较简单，就是打印第一个参数，然后把第二个参数——一组参数传递给`print`，形成递归。假设有如下调用：

```c++
print(1, 2.0, 3.0f, "4");
```

这时，第一个参数对应`int`类型的1，其余参数都被归为第二个的一组参数。打印完1后，调用的函数变成了：

```c++
print(2.0, 3.0f, "4");
```

这时，第一个参数变成了`double`类型的2.0，其余参数被归为第二个的一组参数。以此类推，当打印完`float`类型的3.0时，调用的是`print("4");`，此时只有第一个参数，第二个参数为空，变参模板允许提供的参数为空，此时打印字符串`“4”`。在打印完字符串`“4”`之后，print被再次调用，只是此时已经没有参数了，调用的是`print();`，但这个模板并不能实例化一个接受零个参数的版本，所以需要我们提供一个类似递归出口的版本，即上面的什么也不做的`print`。

在可变函数模板里，还可以调用`sizeof...()`方法查看这一组参数里包含几个参数。

# 可变参数模板特化

可变参数模板比起普通模板多了一种特化方式，即参数分离。同样以`print`为例：

```c++
void print(){}

template<typename T, typename... Ts>
void print(const T& firstArg, const Ts& ... otherArgs) {
	std::cout << firstArg << std::endl;
	print(otherArgs...);
}

template<typename... Ts>
void print(const Ts& ... args) {
	std::cout << "2nd ver" << std::endl;
	print(args...);
}
```

这里我们定义了两个`print`模板，两个模板都有接受可变参数的部分，但相较于第二个模板，第一个模板单独拿出了第一个参数`T& firstArg`，即可以针对一组参数中的第一个参数做特殊处理，所以第一个模板是第二个的特化。