---
layout: post
title:  "C# Fragment : Event and Delegate"
date:   2019-04-19
excerpt: ""
tag:
- C#
- Event
- Delegate
---

# 委托

委托的实质是一个类。

```c#
// 委托定义
delegate ReturnType DelegateName([parameters]);
// 委托声明
DelegateName delegateInstance = new DelegateName();
// 委托赋值
delegateInstance = delegateFunctionName1;
// 绑定/解绑委托
delegateInstance += delegateFunctionName2;
delegateInstance -= delegateFunctionName1;
```

# 事件

事件是封装了委托的类。

当事件被声明为类的内部成员时，它总会被编译为private类型，即无法使用=赋初值，只有+=和-=操作。

```c#
delegate ReturnType DelegateName([parameters]);
// 事件声明
event DelegateName eventName;
// 添加/删除事件
eventName += event1;
eventName -= event2;
```

事件委托与匿名方法、lambda表达式

```c#
delegate ReturnType DelegateName([parameters]);
// 匿名方法
DelegateName delegateInstance = delegate([parameters]){ return something; };
// lambda表达式
DelegateName delegateInstance = ([parameters]) => { return something; };
DelegateName delegateInstance = ([parameters]) => { return something; };
DelegateName delegateInstance =   parameter    => { return something; };
DelegateName delegateInstance =   parameters   =>          something   ;
```

# 应用

Observer Pattern（观察者模式）