---
layout: post
title:  "Operating System： Semaphore"
date:   2019-11-17
excerpt: ""
tag:
- Operating System
---

本文主要参考《计算机操作系统（第四版）》（西安电子科技大学出版社）以及清华大学操作系统公开课（向勇、陈渝），整理操作系统的基本概念，供自己复习查阅。

# 信号量机制

进程控制中最重要的一部分便是协调好进程的并发，控制进程同步，最具体的体现就是处理临界资源。信号量机制便广泛应用在临界资源处理方面。

## 信号量的分类与发展

信号量最初的定义是表示资源的**数目**。

### 整型信号量

顾名思义，这种信号量就是用一个整型量s表示某临界资源的数目。在初始化时，把s初始化为资源的数目，并限制只能通过原子操作**wait(s)**和**signal(s)**来访问，这两个操作通常也被称为**P**、**V**操作。可代码表示如下：

```c++
wait(s) {
    while(s <= 0);
    --s;
}

signal(s) {
    ++s;
}
```

### 记录型信号量

可以看到，在整型信号量中，只要申请不到资源，就会不断调试，并不符合“让权等待”原则（即让进程处于“忙等”状态），而记录型信号量就可以解决这个问题。

在记录型信号量中，简单的整型量s被扩充为记录型结构体，里面额外保存了一个进程表指针，用于链接所有等待进程。

```c++
struct ProcessControlBlock;

using PCB = ProcessControlBlock;
struct Semaphore {
    int value; // 资源数目
    PCB* processList;
};

wait(Semaphore *s) {
    if(--s->value < 0) {
        // 资源已被分配给其他进程，当前进程自我阻塞
        // 并把其插入等待队列
        block(s->processList);
    }
}

signal(Semaphore *s) {
    if(++s->value <= 0) {
        // 条件为真说明有进程在等待队列中，唤醒队列的第一个进程
        wakeup(s->processList);
    }
}
```

可以看到，进程在申请不到资源时会进行自我阻塞，也就符合让权等待的原则。特殊地，在资源初始数量为一时就会转化为互斥信号量。

### AND型信号量

进程互斥只是针对多进程共享一个临界资源的情况。在某些情况下，一个进程需要获得不止一个共享资源后才可以执行任务。这种情况下进程很容易发生**死锁**。如：若两个进程需要的临界资源一致但申请顺序不一致且交替申请，那么他们都得不到所有所需的临界资源，即陷入死锁。

针对这个问题，AND型信号量的解决方案是：将进程在整个运行过程中需要的**所有资源一次性全部分配**给进程，待进程使用完后再一起释放。

```c++
Swait(s1, s2, ..., sn) {
    while(true) {
        if(s1 >= 1 && s2 >= 1 && ... && sn >= 1) {
            --s1; --s2; ...; --sn;
            break;
        } else {
            wait();
        }
    }
}

Ssignal(s1, s2, ..., sn) {
    while(true) {
        ++s1; ++s2; ...; ++sn;
    }
}
```

### 信号量集

由于P、V操作只能对信号量进行加一和减一操作，当一次需要n个单位时显然是低效的，而且逐单位分配更容易引起死锁。为了系统安全，有些情况下我们需要管制资源，具体而言即当所申请的资源数d低于对应的下限值t时不予分配。

针对这个问题，信号量集的解决方案是：在进行P、V操作时需要知道进程对资源的需求量以及对应资源的下限值，并由此决定资源的分配。

```c++
Swait(s1, t1, d1, ..., sn, tn, dn);
Ssignal(s1, d1, ..., sn, dn);
```

三种特殊的信号量集：

1. `Swait(s, 0, 0)`：信号量集中只有一个信号量，且下限值和允许申请数相同；
2. `Swait(s, 1, 1)`：此时的信号量集退化为记录型信号量；
3. `Swait(s, 1, 0)`：当s＞0时，允许多个进程进入某个特定区，当s变为0时封锁区域，作用类似于开关。

## 信号量的应用

### 实现进程互斥

要实现某个资源的互斥访问，只需要为该资源设置一个互斥信号量mutex并设初值为1，结合P、V操作即可。

```c++
Semaphore mutex = 1;

// a进程P资源
Pa() {
    while(true) {
        wait(mutex);
        // 临界区
        signal(mutex);
        // 剩余区
    }
}
```

### 实现前趋关系

> 前趋图是一种描述进程前趋关系的**有向无环图**。

利用信号量可以描述进程之间的前趋关系。有前趋图如下：

![M08tun.png](https://s2.ax1x.com/2019/11/16/M08tun.png)

那么可以用如下代码框架描述这个关系：

```c++
Semaphore a, b, c, d, e, f, g;

p1() { s1; signal(a); signal(b); }
p2() { wait(a); s2; signal(c); signal(d); }
p3() { wait(b); s3; signal(e); }
p4() { wait(c); s4; signal(f); }
p5() { wait(d); s5; signal(g); }
p6() { wait(e); wait(f); wait(g); s6; }

main() {
    a.value = b.value = c.value = d.value = 0;
    e.value = f.value = g.value = 0;
    cobegin
        p1(); p2(); p3(); p4(); p5(); p6();
    coend
}
```

## 经典同步问题

### 生产者-消费者问题

有一群生产者进程在生产产品，并将产品提供给消费者进程消费。为了使生产者和消费者能并发执行，在两者之间设置了一个具有n个缓冲区的缓冲池。这也就暗含两个约束关系：消费者不能到一个空缓冲区取产品；生产者不能往一个满缓冲区投放产品。

利用**记录型信号量**即可解决这一问题。这里需要设置两个信号量：`empty`和`full`表示缓冲区的两个临界状态，用代码描述如下：

```c++
int in = 0, out = 0;
Product pool[n]; // 环形缓冲池
Semaphore mutex = 1, empty = n, full = 0;

void Producer() {
    while(true) {
        Produce(); // 生产产品
        wait(empty); // 需要一个空的缓冲区容纳新产品
        wait(mutex); // 请求锁
        pool[in] = nextp; // 存放新产品
        in = (in + 1) % n; // 偏移指针指向下一个可存放区域
        signal(mutex); // 释放锁
        signal(full); // 增加一个满缓冲区
    }
}

void Consumer() {
    while(true) {
        wait(full); // 需要一个满缓冲区来取其中的产品
        wait(mutex); // 请求锁
        nextc = pool[out]; // 取出产品
        out = (out + 1) % n; // 偏移指针指向下一个可取出区域
        signal(mutex); // 释放锁
        signal(empty); // 增加一个空缓冲区
        Consume(); // 消费取出的产品
    }
}

void main() {
    cobegin
		Producer();
		Consumer();
    coend
}
```

需要注意的是，对互斥信号量的申请一定要出现在对资源信号量的申请之后，否则可能引起死锁。显然这个问题也可以用**AND型信号量**解决。

```c++
int in = 0, out = 0;
Product pool[n];
Semaphore mutex = 1, empty = n, full = 0;

void Producer() {
    while(true) {
        Produce();
        Swait(empty, mutex); // 要同时申请到空缓冲区和互斥信号量
        pool[in] = nextp;
        in = (in + 1) % n;
        Ssignal(mutex, full); // 同时释放满缓冲区和互斥信号量
    }
}

void Consumer() {
    while(true) {
        Swait(full, mutex);
        nextc = pool[out];
        out = (out + 1) % n;
        Ssignal(mutex, empty);
        Consumer();
    }
}
```

### 哲学家进餐问题

有五个哲学家共用一张圆桌，桌上放着5个碗和5只筷子，哲学家只做两件事：思考、进餐。一个哲学家总是在思考，饥饿时也只能拿离他最近的两只筷子，拿到了才能进餐。进餐结束才继续思考。

利用**记录型信号量**可以解决这一问题。

```c++
Semaphore chopstick[5] = {1, 1, 1, 1, 1};

while(true) {
    Think();
    wait(chopstick[i]);
    wait(chopstick[(i + 1) % 5]);
    Eat();
    signal(chopstick[i]);
    signal(chopstick[(i + 1) % 5]);
}
```

但上面的做法会有一个问题：如果5位哲学家都处于饥饿状态，那么他们都会先去拿自己左手边的筷子，引起死锁。可以制定一些规则解决死锁问题。而用**AND型信号量**则不用考虑死锁问题。

```c++
Semaphore chopstick[5] = {1, 1, 1, 1, 1};

while(true) {
    Think();
    Swait(chopstick[i], chopstick[(i + 1) % 5]);
    Eat();
    Ssignal(chopstick[i], chopstick[(i + 1) % 5]);
}
```

### 读者-写者问题

一个数据文件可被多个进程共享，只对该文件做读操作的称为Reader进程，其他进程称为Writer进程。多个读进程可以同时读取文件，但当有写进程访问该文件时，其他进程（不管是读进程还是写进程）都不能访问该文件。这个问题常用于测试新的同步原语。

利用**记录型信号量**可以解决这个问题。

```c++
Semaphore rMutex = 1, wMutex = 1;
int readerCount = 0;

void Reader() {
    while(true) {
        wait(rMutex); // 申请读锁
        if(readerCount == 0) wait(wRmutex); // 如果当前没有读者，那就需要锁写入
        ++readerCount;
        signal(rMutex); // 由于读取可以同时进行，所以现在就释放读锁
        Read();
        wait(rMutex);
        --readerCount;
        if(readerCount == 0) signal(wMutex); // 没有读者才释放写锁
        signal(rMutex);
    }
}

void Writer() {
    while(true) {
        wait(wMutex);
        Write();
        signal(wMutex);
    }
}

void main() {
    cobegin
		Reader();
		Writer();
    coend
}
```

如果文件要求最多可以被RN个读进程访问，那么用**信号量集**解决问题更方便。

```c++
int RN;
Semaphore L = RN, mutex = 1;

void Reader() {
    while(true) {
        Swait(L, 1, 1);
        Swait(mutex, 1, 0);
        Read();
        Ssignal(L, 1);
    }
}

void Writer() {
    while(true) {
        Swait(mutex, 1, 1,  L, RN, 0);
        Write();
        Ssignal(mutex, 1);
    }
}

void main() {
    cobegin
		Reader();
		Writer();
    coend
}
```

