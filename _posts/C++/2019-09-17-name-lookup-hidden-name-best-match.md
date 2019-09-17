---
layout: post
title:  "C++：Name Lookup & Best Match"
date:   2019-09-17
excerpt: ""
tag:
- C++
- OOP
---

# 名字查找

每当一个变量或者一个对象出现，编译器都会进行**名字查找（name lookup）**，以确认这个变量或对象的具体属性。一般情况下，程序会从变量出现的地方开始向上查找，由内向外查找各级作用域直到全局作用域，找到同名的变量声明即停止，若最终没找到则报错。

```c++
#include <iostream>
using namespace std;

int a = 0;

int main() {
	string a = "1";
	cout << a << endl; // 在main函数体内找到了a的声明，停止名字查找，输出为string的内容“1”
	return 0;
}
```

名字查找还受到作用域限定符`::`的影响，若变量之前出现了作用域限定，那么则会从限定的作用域内进行全局搜索，并且不会查找其他位置。如果`::`之前没有出现作用域名，那么就会在全局作用域查找。

```c++
#include <iostream>
using namespace std;

int a = 0;

int main() {
	string a = "1";
	cout << ::a << endl; // 限定a的查找从全局作用域开始，那么首先被找到的是a的int类型定义，程序输出0
	return 0;
}
```

此外，由于在找到同名声明之后便会停止查找，**函数匹配出现在名字查找之后**。

```c++
#include <iostream>
using namespace std;

void print() { cout << "1" << endl; }

int main() {
	int print;
	print(); // 试图调用print()，但程序无法通过编译，这是因为在找到int型变量print之后，
             // 编译器就停止了查找，把一个变量当作函数调用显然是错误的
	return 0;
}
```

# 名字隐藏

从上面的例子可以看出，在不使用访问限定符的情况下，内层作用域的变量会隐藏外层作用域的同名变量，即发生了**名字隐藏**。名字隐藏还会发生在类的继承过程，在子类定义和父类同名的成员时，会隐藏从父类继承来的成员，名字隐藏并不是什么特性，相反，名字隐藏会增加程序编写的难度。我们应该**养成良好的命名习惯，避免出现名字隐藏**。

# 最佳匹配

在函数调用时，首先对函数进行名字查找，在找到一个同名函数（假设不会出现命名冲突）时，编译器并不会停止查找，而是继续找出当前作用域的所有重载函数，然后根据最佳匹配规则，确定实际调用的函数。不同作用域的同名函数并不会被视为重载。最直观的例子是，类内的函数与类外的同名函数并不会形成重载。

在名字查找完成且不存在编译错误的情况下，编译器便开始确定与调用匹配得最好的函数。**最佳匹配原则**也很简单，实参类型与形参类型越接近，匹配得越好。

```c++
void print(int a) { cout << "int" << endl; }

void print(double a) { cout << "double" << endl; }

print(1); // 1是int类型，与print的int形参重载构成最佳匹配，故程序输出“int”
```

但如果同时有多个函数与调用形成匹配，那么编译器将报错。编译器不允许有二义性的调用存在。

```c++
void print(int a, int b, int c) { cout << "int" << endl; }

void print(double a, double b, double c) { cout << "double" << endl; }

print(1, 1.0, 1); // 编译器指出有多个函数重载匹配
```

