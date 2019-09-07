---
layout: post
title:  "C# Fragment：Reflection"
date:   2019-04-21
excerpt: ""
tag:
- C#
- Reflection
---

# Reflection是什么

一般的程序的操作对象是数据（data），但有时候，我们想要在运行期获取有关程序自身的信息，例如我们想在编译器界面有一个类浏览器，这个时候，我们需要获取的就是有关程序集（program assemblies）自身的信息，这些信息我们称为元数据（metadata），程序获取自身或者其他程序集元数据的能力，就是**反射（reflection）**。反射这个词很精准地描述了这个行为的特征，实现反射的程序在编译期是不清楚自身获取的元数据是什么类型的，只能通过运行期元数据的反馈得知这个元数据的具体信息。在C#中，我们可以使用`System.Reflection`命名空间实现反射。

# Type

基类库提供了一个叫`Type`的抽象类，这个类的设计初衷就是描述类型的特征。这个类的对象就是获取程序内的类型在运行时的信息的载体。`Type`是线程安全的。这是微软官方给出的`Type`的定义：

```C#
[System.Runtime.InteropServices.ClassInterface(System.Runtime.InteropServices.ClassInterfaceType.None)]
[System.Runtime.InteropServices.ComVisible(true)]
[System.Serializable]
public abstract class Type : System.Reflection.MemberInfo,
						   System.Reflection.IReflect,
						   System.Runtime.InteropServices._Type
```

可以看到，这是一个抽象类，故运行时获取的信息实际上是储存在`Type`的派生类`RuntimeType`中，但这个引用仍是`Type`类型。在描述上，为了方便起见，我们还是称它为`Type`类型。CLR会为每一个在程序中用到的类都生成一个储存类型信息的Type对象，这个类型的所有实例都指向这个对象。C#的`typeof`运算符以及Visual Basic提供的`GetType()`都可以获取类型信息。

```C#
using System;
using System.Reflection;

class Main {
    static void Main() {
        Type typeString = typeof(string);
        int a = 0;
        Type typeA = a.GetType();
        Console.WriteLine(typeString, typeA);
    }
}
```

`Type`给出了一系列描述、获取类型信息的Field、Property以及Method，下面给出一些常用的Property和Method。

|  Property  |                    Description                     |
| :--------: | :------------------------------------------------: |
|  Assembly  | 声明这个类型的程序集，或者获取这个泛型定义的程序集 |
| Attributes |              这个类型的所有Attribute               |
|    Name    |                   这个类型的名称                   |
| Namespace  |                这个类所在的命名空间                |
|   IsXXX    |              这个类型是否是XXX的真值               |

|      Method      |           Description            |
| :--------------: | :------------------------------: |
| GetConstructor() | 获取这个类型所有的public构造方法 |
| GetProperties()  |   获取这个类型所有的public属性   |
|   GetMethods()   |   获取这个类型所有的public方法   |
|    GetType()     |      获取这个类型当前的类型      |

```C#
using System;
using System.Reflection;

public class Main {
    static void Main() {
         Type type = GetType();
		string name = type.Name;
		Assembly assembly = type.Assembly;
		PropertyInfo[] propertyInfo = type.GetProperties();
		MethodInfo[] methodInfos = type.GetMethods();
		PropertyInfo[] propertyInfos = type.GetProperties();
		FieldInfo[] fieldInfos = type.GetFields();

		print(type);
		print(assembly);
		foreach (var it in fieldInfos) {
			print(it.Name);
		}
    }
}
```

# 应用

可以利用反射获取的程序集信息改变程序的运行。

泛型与反射结合是一种强大的插件工具开发方法。

```C#
using System;
using System.Reflection;

public class Utility<T> where T : class {
    
    private T target;
    private Type type;
    
    private void Instantiate() {
        type = typeof(T);
        ConstructorInfo constructor = type.GetConstructor(Type.EmptyTypes);
        target = (T)constructor.Invoke(null);
    }
    
    private void InvokeMethod(string methodName) {
        MethodInfo method = type.GetMethod(methodName);
        method.Invoke(target，null);
    }
}
```

在上面的代码中我们实现了一个简单的泛型类，它可以在不知道对象类型的情况下，通过反射获取并调用这个类的无参构造函数。`GetConstructor`在Type中有很多重载方法，我们使用的是`GetConstructor(Type[])`重载，这个方法可以让我们获取到这个类的构造参数类型列表为`Type[]`的构造方法，最后通过`_Type`接口提供的`Invoke`方法调用构造函数实例化一个泛型对象并将引用保存在`target`中。`InvokeMethod`的实现也是类似，使用`GetMethod(string)`重载，通过方法名获取方法信息，然后通过`_MethodInfo`接口提供的`Invoke(Object, Object[])`方法调用实例的无参方法。