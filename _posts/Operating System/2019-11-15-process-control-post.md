---
layout: post
title:  "Operating System： Process Control"
date:   2019-11-15
excerpt: ""
tag:
- Operating System
---

本文主要参考《计算机操作系统（第四版）》（西安电子科技大学出版社）以及清华大学操作系统公开课（向勇、陈渝），整理操作系统的基本概念，供自己复习查阅。

# 进程控制

进程控制是进程管理中最基本的功能，一般由内核中的**原语（Primitive）**实现。

## 支撑功能

操作系统会提供给其他模块所需要的一些基本功能，其中包含三个最基本的支撑功能。

### 中断处理

中断处理是内核最基本的功能，是整个操作系统的基础，操作系统中的许多重要活动都依赖于中断，如：系统调用、键盘输入、进程调度、设备驱动。

### 时钟管理

时钟管理是内核的一项基本功能，操作系统中的许多活动需要时钟支撑，如：时间片轮转调度。

### 原语操作

**原语**即由若干命令组成的用于完成一定功能的一个过程，最重要的一点是，它是**原子操作**，即不可拆分的基本单位。原语在被执行的过程中不允许中断。

## 资源管理功能

1. 进程管理
2. 存储器管理
3. 设备管理

## 进程生命周期

### 进程的创建

操作系统允许一个进程创建另一个进程。

在UNIX系统中，由这种创建与被创建引出的是，进程是存在层次关系的，进程与其子孙进程构成了一个进程家族，PCB中包含了一个家族关系表，标明自己的父进程及所有子进程。

而在Windows中不存在层次概念，所有进程都具有相同地位。特殊地，一个进程在创建另一个进程时可以获得一个**句柄（Handle）**，用来控制被创建的进程，而且句柄是可以被传递的。以句柄为基础，进程之间的关系简化为控制与被控制的关系。

#### 引起创建进程的事件

典型的有四类事件会导致一个进程创建另一个进程：

1. 用户登录：在分时系统中，用户登陆成功后系统会为该用户创建一个进程；
2. 作业调度；
3. 提供服务：运行中的用户程序提出某个请求后，系统会专门创建一个进程来提供服务；
4. 应用请求：前三者都是系统为用户创建进程，而这一类是用户进程自己创建新进程。

#### 创建原语

每当系统中出现创建进程的请求，系统就会调用**创建原语Creat**，按以下步骤创建新进程：

1. 为新进程申请获得唯一的数字标识符，申请空白PCB；
2. 为新进程分配其运行所需的资源；
3. 初始化PCB；
4. 将新进程插入就绪队列。

### 进程的终止

#### 引起进程终止的事件

1. 正常结束，即进程的任务已完成，准备退出运行；
2. 异常结束，即进程在运行时发生了某种异常事件，程序无法继续执行，如：越界错、非法指令、权限错、运行超时、等待超时、运算错、I/O故障；
3. 外界干预，干预多种多样，典型的如：操作系统干预、父进程请求、父进程终止。

#### 终止原语

如果系统中发生了要求终止进程的事件，系统就回调用进程的终止原语，并按以下步骤终止进程：

1. 根据被终止进程的标识符，从PCB集合中检索出该进程的PCB，读出进程的状态；
2. 若待终止进程处于执行状态，则终止执行
3. 终止其子孙进程；
4. 将被终止进程拥有的全部资源归还给父进程或操作系统；
5. 将终止进程从所在队列移出。

### 进程的阻塞与唤醒

#### 引起进程阻塞和唤醒的事件

1. 向系统请求共享资源失败；
2. 等待某操作完成；
3. 新数据尚未到达；
4. 等待新任务到达。

#### 阻塞

阻塞是一种**主动行为**。正在执行的进程遇到引起阻塞的事件时，便会调用**阻塞原语Block**将自己阻塞。

#### 唤醒

唤醒是一种**被动行为**。当被阻塞进程期待的事件发生时，有关进程就会调用**唤醒原语wakeup**将等待该事件的进程唤醒。

### 进程的挂起与激活

如之前提到的，操作系统还可以挂起和激活进程，对应**挂起原语suspend**和**激活原语active**。

