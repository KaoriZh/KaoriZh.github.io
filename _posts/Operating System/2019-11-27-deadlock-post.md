---
layout: post
title:  "Operating System： Deadlock"
date:   2019-11-27
excerpt: ""
tag:
- Operating System
---

本文主要参考《计算机操作系统（第四版）》（西安电子科技大学出版社）以及清华大学操作系统公开课（向勇、陈渝），整理操作系统的基本概念，供自己复习查阅。

# 死锁

如果一组进程中的每个进程都在等待仅由该组进程中其他进程才能引发的事件，那么称这一组进程是死锁的。

## 死锁的成因

死锁的成因可以归结为以下三类：竞争不可抢占资源、竞争可消耗资源、进程推进顺序不当。

## 产生死锁的必要条件

产生死锁有四个必备条件，无法同时满足这四个条件就不会产生死锁。

1. 互斥条件：即待分配的资源的使用具有排他性；
2. 请求和保持条件：若进程持有了一个资源的同时又有一个新的资源请求，且该资源已被其他进程占有，此时请求进程被阻塞而已持有的资源又继续保持；
3. 不可抢占条件：进程获得的资源在使用完之前不能被抢占，只能在进程使用完时自己释放；
4. 循环等待条件：即存在一个进程——资源的循环链，构成环路等待。

## 处理死锁的方法

### 预防死锁

由于死锁的形成有4个必要条件，即只要破坏其一，就不会产生死锁。又由于互斥是保证临界资源访问安全的条件，故一般针对其他3个条件预防死锁，即破坏请求和保持条件、不可抢占条件或循环等待条件其一即可。

### 避免死锁

避免死锁同样是事先预防策略，但它的工作在资源动态分配时才展开，并且是通过防止系统进入不安全状态来避免发生死锁。这也是在事先预防阶段的常用方法，因为这种方法施加的限制条件较弱，系统性能较好。

#### 安全状态

在这种方法中，所谓的不安全状态即可能发生死锁的状态。使用该方法时，每当系统要进行资源分配，都会先计算这次资源分配的安全性，若计算结果表示此次分配会使系统进入不安全状态，则令进程等待。所谓的安全状态，即系统可以找到一个安全序列推进进程执行。安全序列是指：对于系统当前所有进程，存在某种进程推进顺序使得每个进程对资源的最大需求能被满足，每个进程最终都可以顺利完成，这个推进顺序即安全序列。

#### 银行家算法

这个算法是最有代表性的避免死锁算法，由Dijkstra提出。银行家算法要求每一个新进程进入系统时都必须申明运行时所需的各种资源的最大数目，且不能超过对应的系统拥有总量，同时要求系统维护四个数据结构：

1. 一个m维向量Available，$Available[j]=K$表示系统中第j类资源的当前可用数目为K，显然该向量的初始值为系统全部资源的可用数目；
2. 一个$n\times m$的矩阵Max，$Max[i][j]=K$表示进程i对第j类资源的最大需求数目为K；
3. 一个$n\times m$的矩阵Allocation，$Allocation[i][j]=K$表示进程i当前已分得的第j类资源数目为K；
4. 一个$n\times m$的矩阵Need，$Need[i][j]=K$表示进程i还需要第j类资源K个才能完成任务。

显然对于这三个矩阵有：$Max[i][j]=Need[i][j]+Allocation[i][j]$。

有了这些数据结构，就可以进行安全性检查了。对于到来的进程$P_i$，设它对资源的请求向量为$Request_i$，$Request_i[j]=K$表示该进程对第j类资源的需求量为K，接下来开始执行检查：

1. 若$Request_i[j] \le Need[i][j]$，则进行下一步，否则认为出错，因为这代表它请求的资源数大于它所需要的资源数；

2. 若$Request_i[j] \le Available[j]$，则进行下一步，否则令进程等待，因为此时资源不足以保证进程的运行；

3. 系统修改数据结构的值（即模拟资源分配）：
   $$
   Available[j]=Available[j]-Request_i[j]\\
   Allocation[i][j]=Allocation[i][j]+Request_i[j]\\
   Need[i][j]=Need[i][j]-Request_i[j]
   $$

4.  系统执行安全性算法，判断资源分配后的系统是否仍是安全状态，若是则正式分配资源，否则模拟分配作废，让进程等待。 

安全性算法需要两个辅助数据结构：

1. 向量Work，表示系统可供给的各类资源的数目，初始状态：$Work=Available$；
2. 向量Finish，表示系统是否有足够的资源分配给进程使之完成，初始状态：对于所有的i，$Finish[i]=false$。

两个向量的维度均与向量Available一致。每一次执行都从进程集合中找到一个可同时满足下列条件的进程：$Finish[i]=false$、$Need[i][j] \le Work[j]$，找到进程后执行：$Work[j]=Work[j]+Allocation[i][j]$以及$Finish[i]=true$，直到没有满足条件的进程时，检查是否所有的进程的Finish均为true，若是则认为系统仍处于安全状态，反之则是不安全。

### 检测、解除死锁

当系统没有针对死锁的预防措施时，系统就需要配备死锁的检测和解除算法。

#### 资源分配图

为了监测死锁，系统需要保存有关资源的请求和分配信息，并且提供一个检测算法。资源分配图可以帮助我们理解死锁的检测机制。资源分配图是一种有向图，结点代表进程或者资源，由资源指向进程的边代表分配一个单位的资源，反之代表释放一个单位的资源。

![Resource Allocation Graph](https://s2.ax1x.com/2019/11/27/Q9LxWF.png)

上图就是一个简单的资源分配图，圆结点代表进程，方框结点代表资源，框内的圆代表资源数目。我们知道，随着进程的推进，进程是可以完成的，也就是说，进程所申请的资源最终肯定会被释放，这也就提供了简化资源分配图的依据。可以找出所有既不阻塞也非独立的进程结点，然后同时去掉与它相连的所有请求边和分配边（即使之孤立）。若所有的进程都可以顺利执行，那么图中的所有边都应被消去，即最终资源分配图是可以被完全简化的；反之，若系统处于死锁状态，那么系统的资源分配图是不可完全简化的。

#### 死锁定理

由于前人已经证明：不同的简化顺序并不会影响最终得到的简化图，这就可以给出一个判断是否存在死锁的定理：系统处于死锁状态的充要条件是当且仅当系统的资源分配图是不可完全简化的。

#### 解除死锁

当检测出死锁时，一般可通过两种方法打破死锁状态：

1. 抢占资源，即掠夺其他进程的资源给死锁进程；
2. 终止进程，即终止系统中若干个死锁进程以打破环路。