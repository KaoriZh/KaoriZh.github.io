---
layout: post
title:  "C# Fragment : Interface"
date:   2019-04-19
excerpt: ""
tag:
- C#
- Interface
---

OOP中另一个重要的概念：**Interface（接口）**。接口在形式上与类相似，但又和类有所区别。接口的抽象定义是：描述一类物体具有的特征。例如：人可以行走，猫可以行走，虽然两者的具体行为不一致，但我们都可以把这个行为抽象成“行走”这个描述。下面是C#编程中的接口特征。

接口是表示一组函数成员而不是成员的引用类型。

接口成员不允许带有访问权限修饰符，显然这（例如一个private的接口）毫无意义，因此这些都是隐式public的。

```c#
// 接口声明(第一个I是出于命名规范，表明这是一个接口而不是类)
public interface IInterfaceName {
    // 函数成员声明
    ReturnType Function([parameters]);
    // ...
}
```

C#不支持多继承，但支持单继承多接口。

如果一个类实现了一个接口，那么它就必须实现这个接口中的所有方法。

```c#
// 一个实现接口的类示例
public class Class : BaseClass, IInterface1, IInterface2, ... {
    // 实现接口1中的方法
    ReturnType FunctionInIInterface1([parameters]) {
        //Do something
    }
    // 实现接口2中的方法
    ReturnType FunctionInIInterface2([parameters]) {
        //Do something
    }
    // ...
}
```

接口对象是引用类型。

不能通过类对象访问接口，但是可以把类对象引用强制转换为接口类型，即可使用`.`调用接口方法。

```c#
// 承接上一段代码的例子
Class classInstance = new Class();
// 此时我们可以通过类对象的引用访问类的成员
classInstance.BaseClassFunction(); // 假设我们集成的这个基类中含有这个方法
classInstance.FunctionInIInterface1([parameters]); // 访问已经实现的接口
// 强制转换为接口的引用
IInterface1 interfaceInstance = (IInterface1)classInstance;
// 此时我们可以通过接口的引用访问接口的成员（绕开了类）
interfaceInstance.FunctionInIInterface1([parameters]);
```

引用转换与`as`运算符

强制转换未实现接口的类引用为接口引用时会报异常，as运算符则会在这个操作返回null。

```c#
IInterface1 interfaceInstance = classInstance as IInterface1;
```

