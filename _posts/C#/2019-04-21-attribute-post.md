---
layout: post
title:  "C# Fragment：Attribute"
date:   2019-04-21
excerpt: ""
tag:
- C#
- Attribute
---

```C#
[Obsolete]
public static void Func() {
    // ...
}
```

# Attribute是什么

注释是对程序源代码的一种说明，在编译时会被编译器丢弃，不会影响程序的执行，但有时候我们希望“注释”可以影响到程序。这个时候，Attribute就派上用场了。Attribute（特性）是一种语言构造（language construct），它允许你向程序集里添加元数据，是程序代码的一部分。

# Attribute的形式

Attribute在形式上像是Property、Method、Class的一个装饰或者标签，但实质上它是一个实例化方式特殊的类，它必须依附在一个target上，它一般安放在这些target的上方，在方括号内声明。获取和使用元数据的程序被称为consumer。一个target可以拥有多个Attribute，Attribute的顺序不会影响效果。

# Attribute的使用

在使用Attribute时，我们可以使用Attribute的全称，也可以使用Attribute的前缀。使用无参Attribute时，可以省略参数列表的括号。

```c#
// both are supported
[Obsolete]
public static void Func() {
    // ...
}
[ObsoleteAttribute]
public static void Func() {
    // ...
}
```

也可以在同一个target上使用多个Attribute。

```C#
[Obsolete]
[Serializable]
public static void Func() {
    // ...
}
```

# Predefined Attribute

.NET为我们提供了一些预定义的Attribute，如：



`[Obsolete]`：需要`using System;`

作用：提示这个方法已经过时，但还没有被系统丢弃，给出warning。

Obsolete的构造方法：`public ObsoleteAttribute(string message = "", bool isError = false)`

其中`message`是反馈给方法调用者的指示信息，`isError`为真时，这个弃用会升级为报错。

```c#
using System;

public class Test {
	// 调用警告
    [Obsolete("Use SuperPrint instead")]
	static void Print() {
		// using System
		Console.WriteLine("Print");
	}
	
	// 调用报错
	[Obsolete("Use SuperPrint instead", true)]
	static void PrintSomething(string something) {
		Console.WriteLine(something);
	}
}
```



`[Conditional]`：需要`using System.Diagnostics`

作用：通过预处理指令选择性编译代码

target：1.返回类型为`void`的方法，但不能是重写方法，可以是虚函数；2.继承自Attribute的类

Conditional的构造方法：`public ConditionalAttribute(string header)`

其中`header`是预编译指令，若这个程序包含header，则编译这个方法，否则完全忽略。

```c#
// 预编译头必须在所有代码之前声明
#define CALL
using System;
using System.Diagnostics;

public class Test {
	// 因为包含了CALL指令，这个方法会被编译
    [Conditional("CALL")]
    public void Call() {
        Console.WriteLine("This Method is being called");
    } 
}
```

# 自定义Attribute

前面提到，Attribute是一种特殊的类，他们都继承自`Attribute`基类，我们也可以通过继承的方式定义自己的Attribute。

```c#
// 自定义Attribute
// 继承System.Attribute基类
// 这个类的名称必须以Attribute为后缀
// 使用不带参数的Attribute时可省略构造方法的括号
// 一个Attribute类通常只需要包含字段，变量以及构造方法，并且至少包含一个公开的构造方法
// - sealed 相当于Java的final
// AttributeUsage可以限制使用此属性的类型，全类型为All，多类型复合用按位或运算符
// AttributeTargets是一个enum类型，里面规定了target的类型以及对应的二进制码
// AttributeUsage的第二个参数规定这个Attribute是否可继承，默认是
// 第三个参数规定一个类型是否可以挂载多个此Attribute的实例，默认否

// 声明了一个叫MyAttri的Attribute，它的target只能是Property或者Method
[AttributeUsage(AttributeTargets.Property | AttributeTargets.Method)]
public sealed class MyAttriAttribute : Attribute {

	private string name;
	private int index;
	
	public MyAttriAttribute(string _name = "", int _index = 0) {
		name = _name;
		index = _index;
	}
}
```

