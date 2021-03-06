---
layout: post
title:  "Operating System： Tutorial"
date:   2019-11-14
excerpt: ""
tag:
- Operating System
---

本文主要参考《计算机操作系统（第四版）》（西安电子科技大学出版社）以及清华大学操作系统公开课（向勇、陈渝），整理操作系统的基本概念，供自己复习查阅。

# 操作系统

**操作系统（Operating System，OS）**是计算机硬件之上的第一层软件，是现代计算机系统中最基本和最重要的系统软件。

## 目标

1. **方便性**：方便用户使用和学习计算机。
2. **有效性**：提高系统资源的利用率，提高系统的吞吐量。
3. **可扩充性**：操作系统由无结构、模块化、层次化发展到微内核结构，微内核方便添加新功能和模块，方便扩充。
4. **开放性**：遵循世界标准规范。

## 作用

1. 操作系统是用户和计算机硬件系统之间的**接口**。
2. 操作系统是计算机系统资源的**管理者**。
3. 操作系统实现了对计算机资源的**抽象**：I/O设备管理软件是第一层抽象，文件管理软件是第二层抽象。

## 分类

### 单道批处理系统（Simple Batch Processing System）

把需要一批处理的作业以脱机方式输入到磁带上，配合监督程序，一个接一个处理。**缺点**：系统资源得不到充分利用。

### 多道（Multiprogrammed）批处理系统

为**提高资源利用率和系统吞吐量**而提出。用户提交的作业放在“后备队列”（外存）中等待，由作业调度算法从后备队列选择若干作业调入内存，它们共享CPU和系统资源。**优点**：资源利用率高，系统吞吐量大，平均周转时间长。**缺点**：无交互能力。

### 分时系统（Time Sharing System）

为满足**人机交互**而提出。在以往的操作系统中，作业首先驻留在外存，即便调入内存也要等待后才能执行，完全无法实现交互。

#### 关键

1. 及时接收用户键入的命令或数据：为每个终端配备**缓冲区**暂存键入内容。
2. 及时处理用户键入的命令和数据：由于作业在磁盘上无法运行，故**直接进入内存**；引入**时间片**概念，规定作业运行的时间，基于时间片轮转算法，多作业轮转运行。

### 实时系统（Real Time System）

实时的含义是：及时且实时的计算，即计算结果必须及时给出，且结果的正确性取决于产生结果的时间。

### 微机操作系统

配置在微型机上的操作系统。

#### 分类

1. 单用户单任务操作系统，例：CP/M
2. 单用户多任务操作系统，例：Windows
3. 多用户多任务操作系统，例：UNIX

## 基本特性

1. 程序**并发**执行；引入**进程**作为系统中能独立运行的资源分配的基本单位。
2. 资源共享，又称资源复用，即系统中的资源可供内存中的多个并发执行的进程共同使用。
3. **虚拟**，即利用**时分复用**或**空分复用**将一个物理实体变为若干个逻辑上的对应物。
4. 异步，即进程是以不可预知的速度向前推进的。

> 并发：两个或多个事件在同一时间间隔发生
>
> 进程：由一组机器指令、数据和堆栈等组成，是一个能独立运行的活动实体。

## 主要功能

### 处理机管理功能

1. 进程控制：为作业创建进程、终止已结束进程、控制进程切换
2. 进程同步：**信号量**机制
3. 进程通信：：**消息队列**
4. 调度：作业调度（微机上没有）、**进程调度**

### 存储器管理功能

1. 内存分配：静态、动态
2. 内存保护：界限寄存器
3. 地址映射：需要硬件支持
4. 内存扩充：虚存、请求调入、置换

### 设备管理功能

1. 缓冲管理：单缓冲、双缓冲、缓冲池
2. 设备分配
3. 设备处理：设备驱动

### 文件管理功能

1. 文件存储空间管理
2. 目录管理
3. 文件读/写管理和保护