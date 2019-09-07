---
layout: post
title:  "Prime & Euler Function & Productive Function"
date:   2019-07-08
excerpt: "NULL"
tag:
- Algorithm
---

# 素数筛

## 朴素算法

一般来说，可以用试除法判断某一个数是不是素数：

```c++
bool isPrime(int n) {
    if(n < 2) return false;
    for(int i = 2; i < n; i++)
        if(n % i == 0) return false;
    return true;
}
```

但其实我们只需要试除到根号n即可，因为对于任意的n，假设存在一个大于根号n的因数，那么肯定存在一个小于根号n的因数与之对应。那么有：

```c++
bool isPrime(int n) {
    if(n < 2) return false;
    int m = sqrt(n + .5);
    for(int i = 2; i <= m; i++)
        if(n % i == 0) return false;
    return true;
}
```

## 埃氏筛

但如果我们要求所有小于等于n的数是不是素数呢？这时我们用素数筛法解决。筛法是一种思想，利用之前处理过的信息来更新后面的结果。对于一个大于1的数，它的倍数显然是合数，那么我们可以在遍历到i时，筛去所有i的倍数；当我们遍历到i+1时，只要它还没有被筛去，那它就一定是素数。这就是**埃拉托斯特尼筛法**，简称**埃氏筛**。

```c++
int prime[M];
bool vis[M];
void eratosthenes() {
    for (int i = 2; i < M; i++) {
        if (!vis[i]) {
            prime[++prime[0]] = i;
            for (int j = 2 * i; j < M; j += i) {
                vis[j] = 1;
            }
        }
    }
}
```

## 改进埃氏筛

显然埃氏筛有很多的重复的筛除操作，把朴素算法中的优化引入埃氏筛，我们就可以得到改进的埃氏筛。

```c++
int prime[M];
bool vis[M];
void eratosthenes_plus() {
    int m = sqrt(M + .5);
    for (int i = 2; i <= m; i++) {
        if (!vis[i]) {
            prime[++prime[0]] = i;
            for (int j = i * i; j < M; j += i) {
                vis[j] = 1;
            }
        }
    }
}
```

## 欧拉筛

虽然改进的埃氏筛复杂度在大多数情况已经可以被接受了，但有时我们需要线性时间判断1~n的所有素数。这时就需要欧拉筛。欧拉筛通过保证每个素数都会被它最小的那个（质）因数筛掉，进而使复杂度达到线性。

```c++
int prime[M];
bool vis[M];
void euler() {
    for(int i = 2; i < M; i++) {
        if(!vis[i]) {
            prime[++prime[0]] = i;
        }
        for(int j = 1; j <= prime[0] && i * prime[j] < M; j++) {
            vis[i * prime[j]] = 1;
            if(i % prime[j] == 0) {
                break;
            }
        }
    }
}
```

重点就在于**if(i % prime[j] == 0)**这一句，为什么要在i是第j个素数的倍数时停止筛除呢？
$$
\begin{align}
& 当i是prime[j]的倍数时，我们可以把i表示为i=k\times prime[j]，k\in N^+。\\
& 对于下一个被筛去的数X，X=i\times prime[j+1]=k\times prime[j]\times prime[j+1]，\\
& 显然X还存在一个更小的质因子prime[j]，为了符合每个数都被最小的质因数筛去的原则，\\
& 此时应该跳出循环，不用prime[j+1]\times(k\times prime[j])筛去X，而应该在将来，\\
& 用prime[j]\times(k\times prime[j+1])筛去X。
\end{align}
$$

# 欧拉函数与欧拉定理

## 欧拉函数

在数论中，对正整数n，欧拉函数φ（n）的值为小于或等于n的正整数中与n互质的数的个数。

例如：φ（8）=4，因为1，3，5，7与8互质。

扩展：在群论中，欧拉函数实际上是模n的同余类构成的乘法群的阶。

## 欧拉定理

在数论中，欧拉定理为：若gcd（a，n）=1，则：
$$
a^{\phi(n)}\equiv 1(mod\space n)
$$
欧拉函数的性质和拉格朗日陪集定理结合构成了欧拉定理的证明。

推广：欧拉降幂公式
$$
a^b\equiv a^{b\%\phi(n)+\phi(n)}(mod\space n)
$$
<!--证明链接：https://blog.csdn.net/weixin_38686780/article/details/81272848#%E6%AC%A7%E6%8B%89%E9%99%8D%E5%B9%82%E5%85%AC%E5%BC%8F -->

例题：洛谷 5091

## 性质

### 基本性质

若n是质数p的k次幂，那么有：
$$
\phi(n)=\phi(p^k)=p^k-p^{k-1}=(p-1)p^{k-1}\\
p^{k-1}为1到n中p的倍数，显然这些数与n不互质。
$$
例：
$$
\phi(72)=\phi(2^3\times 3^2)=2^{3-1}\times (2-1)\times 3^{2-1}\times (3-1)=24
$$

### 费马小定理的推广

回顾费马小定理：若p为质数，a为任意正整数，那么：
$$
a^p-a可以被p整除。
$$
即：
$$
\begin{aligned}
&a^p-a\equiv0(mod\space p)\\
\Leftrightarrow&a^p\equiv a(mod\space p)\\
\Leftrightarrow&a^{p-1}\equiv 1(mod\space p)
\end{aligned}
$$
回到欧拉定理，若p为质数，φ（p）=p-1，那么有：
$$
a^{p-1}\equiv 1(mod\space p)
$$
这就是费马小定理。历史上，欧拉首先证出了费马小定理，然后在这个基础上推广得到了欧拉定理。

### 积性函数

即若m，n互质，那么有φ（mn）=φ（m）φ（n）

### 推广：小于n的所有与n互质的数的和为n*φ（n）/2。

对任意a>b>0，gcd（a，b）=1，总有gcd（a，a-b）=1。那么对于n，有φ（n）个小于n的数与n互质，设其为x，那么总存在一个n-x也与n互质，两者之和为n，那么一共有φ（n）/2对。这些数的和为n*φ（n）/2。

#### 例：hdu 3501

题意：求比n小的、和n不互质的数的和%1,000,000,007，其中n≤10的9次方。

## 欧拉函数计算

对于任意正整数n，分解质因数得：
$$
n=p_1^{k_1}\times p_2^{k_2}\times ......\times p_m^{k_m}
$$
由：
$$
\phi(p^k)=p^k-p^{k-1}=p^k(1-{1\over p})
$$
得：
$$
\phi(n)=p_1^{k_1}(1-{1\over p_1})\times p_2^{k_2}(1-{1\over p_2})\times ......\times p_m^{k_m}(1-{1\over p_m})
$$
又：
$$
n=\prod_{i=1}^m p_i^{k_i}
$$
所以：
$$
\phi(n)=n\times \prod_{i=1}^m (1-{1\over p_i})
$$
代码实现如下：（例题：poj 2407）

```c++
// 计算单个欧拉函数值
int euler(int n) {
    int ans = n;
    // 追求更高效率还可以结合素数表
    int m = sqrt(n + .5);
    for(int i = 2; i * i <= n; i++) {
        if(n % i == 0) {
            ans -= ans / i;
            while(n % i == 0) n/= i;
        }
    }
    if(n > 1) ans -= ans / n;
    return ans;
}
```

下面是打表写法。

```c++
LL euler[N];
void cal_euler() {
	euler[1] = 1;
	for(int i = 2; i <N; i++) {
		if(!euler[i]) {
			for(int j = i; j < N; j += i) {
				if(!euler[j]) euler[j] = j;
				euler[j] -= euler[j] / i;
			}
		}
	}
}
```

### 例题：hdu 2824

题意：欧拉前缀和。注意空间限制。

```c++
#include <algorithm>
#include <iostream>
using namespace std;
typedef long long LL;
const int N = 3e6 + 100;
LL euler[N];
void pre() {
	euler[1] = 1;
	for(int i = 2; i <N; i++) {
		if(!euler[i]) {
			for(int j = i; j < N; j += i) {
				if(!euler[j]) euler[j] = j;
				euler[j] = euler[j] / i * (i - 1);
			}
		}
	}
	for(int i = 2; i < N; i++) {
		euler[i] += euler[i - 1];		
	}
}
int main() {
	pre();
    int a, b;
    while(cin >> a >> b) {
    	cout << euler[b] - euler[a - 1] << endl;
    }
    return 0;
}
```

# 积性函数

## 定义

在数论中，积性函数是指定义在正整数集上的算数函数f（n），且有：f（1）=1；若gcd（a，b）=1，f（ab）=f（a）f（b）。

扩展：完全积性函数：若积性函数f在gcd（a，b）≠1时，仍有f（ab）=f（a）f（b），那么f称为完全积性函数。在数论外的积性函数一般是指完全积性函数。

## 性质

对n质因分解得：
$$
n=\prod^k_{i=1} p_i^{a_i}，其中，p_i为分解得到的质因数
$$
那么对于积性函数f，有：
$$
f(n)=\prod^k_{i=1}f(p_i^{a_i})
$$

## 常见的积性函数

欧拉函数、莫比乌斯函数、gcd（n，k）（k固定），约数函数σ（σ（n）为n的约数个数）。约数个数函数σ定义如下：

对于任意正整数n，设其质因分解为：
$$
n=p_1^{k_1}\times p_2^{k_2}\times ......\times p_m^{k_m}
$$
那么其因数个数为：
$$
N=(k_1+1)\times(k_2+1)\times...\times(k_m+1)
$$

$$
即：σ(n)=\prod_{i=1}^m (k_i+1)
$$

