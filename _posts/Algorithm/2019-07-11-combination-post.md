---
layout: post
title:  "Permutation & Combination"
date:   2019-07-11
excerpt: "NULL"
tag:
- Algorithm
---

# 组合计数

组合数学主要是研究一组离散对象满足一定条件的安排的存在性、构造及计数问题。计数理论是狭义组合数学中最基本的一个研究方向，主要研究的是满足一定条件的排列组合及计数问题。组合计数包含计数原理、计数方法、计数公式。

# 组合计数基本原理

## 加法原理

$$
如果一个目标的实现可以在n种不同的情况下完成，且对于第i种情况又有m_i种不同的方法，\\
那么总的方法数N为：N=m_1+m_2+...+m_n=\sum_{i=1}^n m_i
$$

其中，每种条件达成都能单独实现目标，而不依赖其他条件；任意情况的任两种方法都是唯一的。

## 乘法原理

$$
如果一个目标的实现需要经过n个步骤，对于第k步有m_k种不同的方式实现，\\
那么总的方法数N为：N=m_1\times m_2\times...\times m_n=\prod_{i=1}^n m_i
$$

其中，步骤之间可能存在拓扑关系，但每个步骤都必不可少；每一步内选择何种方式不受其他步骤影响。

## 容斥原理

组合计数最重要的事**不重复**、**不遗漏**。但一般情况下总会出现很多的重复计算，又或者在分类讨论中遗漏某种情况。这时我们就需要容斥原理。容斥原理的基本思想是：先不考虑重复，得出所有的情况数，然后再排除重复计算的部分，由于每一步都有理可循，正确运用即可做到不遗漏不重复。

公式：
$$
设S是有限集合，A_i \subseteq S，i\in N^+，则\\
|\bigcup_{i=1}^n A_i |=\sum_{k=1}^n (-1)^{k-1}\sum_{1\le i_1<i_2<...<i_k\le n} |A_{i_1}\bigcap A_{i_2}\bigcap ...\bigcap A_{i_k}|
$$
容斥公式可以由如下集合运算的基本公式（德摩根公式）以及数学归纳法证明得到：
$$
\overline{\bigcup_{i=1}^n A_i}=\bigcap_{i=1}^n\overline{A_i}\\
\overline{\bigcap_{i=1}^n A_i}=\bigcup_{i=1}^n\overline{A_i}
$$

# 组合计数基本公式

## 排列数公式

从n个不同元素中任取m（m≦n）个元素排成一列（考虑元素先后出现次序），叫做从n个不同元素中取出m个元素的一个排列。排列的总数即为排列数，即叫做从n个不同元素中取出m个元素的排列数（number of permutations）。排列数用符号P（Permutation）或者A（Arrangement）表示。
$$
A_n^m(P_n^m)表示从n个元素里取出m个并排列（要考虑顺序）得到的方案数。\\
A_n^m=n(n-1)...(n-m+1)={n!\over(n-m)!}
$$

<!--组合数的母函数即多项式，可以用对应的母函数解释组合数。-->

### 拓展

$$
\begin{align}
& 1.循环排列：又称圆排列，指从n个元素里取出m个元素构成循环排列的排列数N，N={A_n^m\over m} \\
& 2.把n个元素分成k类，第i类元素的个数为n_i，此时n个元素的排列数N={n!\over \prod_{i=1}^k n_i!}\\

\end{align}
$$

## 组合数公式

从n个不同元素中任取m（m≦n）个元素构成一组（不考虑顺序），叫做从n个不同元素中取出m个元素的一个组合。组合的总数即为组合数，即叫做从n个不同元素中取出m个元素的组合数（number of combinations）。组合数用符号C表示：
$$
C_n^m表示从n个元素里取出m个构成一组（不考虑顺序）得到的方案数，也可以用二项式系数（_n^m）表示。\\
C_n^m={A_n^m\over m!}={n!\over (n-m)!m!}
$$

### 拓展

$$
k类元素，每类元素个数均为无限，从这些元素中取出m个的组合数为C_{k+m-1}^m
$$

### 组合数的性质

$$
\begin{align}
& 1.C_n^m=C_n^{n-m}\\
& 2.mC_n^m=nC_{n-1}^{m-1}\\
& 3.组合数递推式：C_n^m=C_{n-1}^{m-1}+C_{n-1}^m \\
& 4.二项式系数：\sum_{i=0}^n C_n^i=C_n^0+C_n^1+...+C_n^n=2^n \\
&\space\space C_n^0+C_n^2+...=C_n^1+C_n^3+...=2^{n-1} \\
& 5.C_m^m+C_{m+1}^m+...+C_{m+n}^m=C_{m+n+1}^{m+1} \\
& 6.C_n^0+C_{n+1}^1+...+C_{n+m}^m=C_{n+m+1}^m\\
& 7.C_{2n}^2=2C_n^2+n^2\\
\end{align}
$$

<!--https://blog.csdn.net/shadandeajian/article/details/82084087 -->

<!--对于第3点：等式左边表示从n个元素中选取m个元素，而等式右边表示这一个过程的另一种实现方法：任意选择n中的某个备选元素为特殊元素，从n中选m个元素可以由此特殊元素的被包含与否分成两类情况，即m个被选择元素包含了特殊元素和m个被选择元素不包含该特殊元素。前者相当于从n-1个元素中选出m-1个元素的组合，即c(m-1,n-1)；后者相当于从n-1个元素中选出m个元素的组合，即c(m-1,n)。-->

## 错排公式

错排问题也是组合数学的经典问题之一。错排即错误的排列：对于n个元素构成的一种排列，对其重新排序使得每一个元素都不在原来的位置上，这样的排列就称为原排列的一个错排。n个元素的一个排列的错排数记为D（n），则：
$$
\begin{align}
&D(n)\\
=&n!\left[{1\over0!}-{1\over1!}+{1\over2!}-...+{\left( -1^n \right) \over n!} \right]\\
=&n!\cdot\sum_{i=0}^n{(-1)^i\over i!}=n!\cdot\sum_{i=2}^n{(-1)^i\over i!}\\
=&\left[{n!\over e}+0.5\right](取整，由{1\over e}的展开式推出)
\end{align}
$$
<!--运用容斥原理，正整数1, 2, 3, ……, n的全排列有 n! 种，其中第k位是k的排列有 (n-1)! 种；当k分别取1, 2, 3, ……, n时，共有n*(n-1)!种排列是至少放对了一个的，由于所求的是错排的种数，所以应当减去这些排列；但是此时把同时有两个数不错排的排列多排除了一次，应补上；在补上时，把同时有三个数不错排的排列多补上了一次，应排除；……；继续这一过程，得到错排的排列种数为D(n)。-->

# 组合计数常用技巧

## 捆绑法

当要求某些元素必须相邻时，先把他们看作一个整体，然后把整体当成一个元素和其他元素一起考虑。要注意：整体内部也有顺序。

## 插空法

当要求某些元素不能相邻时，可以先把其他元素排好，然后再把要求不相邻的元素插入到已排好的元素的空隙或两端。

## 隔板法

在解决若干相同元素分组问题时，若要求每组至少一个元素，则可以转化为在排成一列的这些元素中插入组数减1个“隔板”，达到分组目的。
$$
例：n个小球m个盒子，每个盒子不为空，则有C_{n-1}^{m-1}种方案；若盒子不要求非空，\\
则可由引入m个球，隔板法解决后从每个盒子里各取走一个球，方案数为C_{n+m-1}^{m-1}
$$

## 例题

1. A、B、C、D、E五人排成一排，其中A、B不站一起，一共有多少种站法？
   $$
   插空法：因为A、B不能站一起，所以我们可以考虑先给C、D、E排序，方案数为A_3^3；\\3人排好序后留出4个空位，A、B插空，方案数为A_4^2，根据乘法原理，总方案数为A_3^3\cdot A_4^2。
   $$

2. A、B、C、D、E五人排成一排，其中A、B必须站一起，一共有多少种站法？
   $$
   捆绑法：既然A、B必须站在一起，那么我们索性把A、B“捆绑”，当做一个整体看待，\\
   然后同等看待这个整体和C、D、E。对于A、B的内部来说，方案数为A_2^2；\\
   对于外部，方案数为A_4^4。总方案数为：A_2^2\cdot A_4^4。
   $$

3. 一张节目表上有3个节目，保持其相对顺序不变，再加入2个新节目，有多少种方案？
   $$
   \begin{align}
   &捆绑法+插空法：\\
   &分两种情况：\\
   &1.两个新节目相邻，那么3个节目有4个空，再考虑内部顺序，总方案数为C_4^1\cdot A_2^2；\\
   &2.两个节目不相邻，2个节目插4个空，方案数为A_4^2；\\
   &那么总方案数即C_4^1\cdot A_2^2+A_4^2。
   \end{align}
   $$

4. 将8个完全相同的球放到3个不同的盒子中，要求每个盒子至少放一个球，有多少种方法？
   $$
   隔板法：把8个球排成一列，球之间的空位数为7，假设我们现在有两个隔板，\\
   在7个空位插入两个隔板，就能把球分成有序的三组，分别对应三个盒子，方案数为C_7^2。
   $$




5. (hdu 6397)题意：给三个数n、m、k， 在0~n-1中选出m个数排成一排使得他们的和等于k，这m个数可以相同，只要排列不同即可。求一共有多少种排列方式是满足题意的。

$$
类似盒子可空的n球m盒问题。不考虑数字的大小限制，总方案数为C_{k+m-1}^{m-1}。\\
设数字为x，有x_1+x_2+...+x_m=k； \\
令y_i=x_i+1，用隔板法分析：y_1+y_2+...+y_m=k+m。\\
但是题目限制数的大小只能在n以内，那么可以这样考虑：\\
假设在我们的枚举中有i个数超出限制，即在m个数里，有x个大于等于n，那么这时的方案数为：\\
C_m^i\cdot C_{k+m-1-i\times n}^{m-1}，对应的分析为：\\
x_1'+x_2'+...+x_m'=k-i\times n。\\
最后结合容斥奇减偶加就能得出答案。
$$

```c++
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;

const int maxn = 2e5 + 100;
const int mod = 998244353;

ll inv[maxn], F[maxn], Finv[maxn];

void init() {
    inv[1] = 1;
    for(int i = 2; i < maxn; i++)
        inv[i] = (mod - mod / i) * inv[mod % i] % mod;
    F[0] = Finv[0] = 1;
    for(int i = 1; i < maxn; i++) {
        F[i] = F[i - 1] * i % mod;
        Finv[i] = Finv[i - 1] * inv[i] % mod;
    }
}

ll C(ll n, ll m) {
    if(n < 0 || m < 0 || m > n) return 0;
    return F[n] * Finv[m] % mod * Finv[n - m] % mod;
}

int main() {
    int t, n, m, k;
    init();
    cin >> t;
    while(t--) {
        cin >> n >> m >> k;
        ll ans = 0;
        for(int i = 0; i * n <= k; i++) {
        	int r = C(m, i) * C(k - i * n + m - 1, m - 1) % mod;
            if(i & 1) ans = (ans - r + mod) % mod;
            else ans = (ans + r) % mod;
        }
        cout << ans << endl;
    }
    return 0;
}
```

6. (uva 10943)求把k个不超过n的非负整数加起来使得和为n的方案数。盒子可空隔板法。因为数据范围比较小，所以直接dp预处理出所有结果，O（1）查询即可。

   ```c++
   #include<bits/stdc++.h>
   using namespace std;
   const int mod = 1e6;
   int n, k, dp[111][111]; // dp[i][j]表示用j个数使得和为i的方案数
   int main() {
   	for (int i = 1; i <= 100; i++) {
   		dp[i][1] = 1;
   		dp[1][i] = i;
   	}
   	for (int i = 2; i <= 100; i++)
   		for (int j = 2; j <= 100; j++)
   			dp[i][j] = (dp[i - 1][j] + dp[i][j - 1]) % mod;
   	while (cin >> n >> k && (n|k)) { 
           cout << dp[n][k] << endl;
   	}
       return 0;
   }
   ```

7. (CF GYM 100548)n盆花排成一列用m种颜色涂，要求相邻颜色不同，且使用颜色数恰为k。求方案数%1e9+7。

   分析：乘法原理+容斥。
   $$
   \begin{align}
   &首先从m种颜色里选出k种备选颜色，方案数为C_m^k；\\
   &乘法原理：对于第一盆花，有k种选择，对于之后的每一盆花，为了保证不重复，只有(k-1)种选择，\\
   &方案数为k\times (k-1)^{n-1}。\\
   &但这时包含了许多使用颜色额数不足k的方案，需要容斥原理排除。\\
   &考虑从k种颜色里取i种涂色，方案数为C_k^i\cdot p(p-1)^{n-1}，结合容斥奇减偶加，可以得出：\\
   &总方案数N=C_m^k\cdot k(k-1)^{n-1}+\sum_{i=2}^{k-1}(-1)^iC_k^i\cdot i(i-1)^{n-1}\\
   &显然需要用到逆元，组合数，快速幂，下面是标程。
   \end{align}
   $$

   ```c++
   #include <iostream>
   using namespace std;
   typedef long long ll;
   
   const int mod = 1e9 + 7;
   const int maxn = 1000010;
   
   ll ans, inv[maxn], F[maxn], Finv[maxn];
   
   ll qpow(ll a, ll b) {
       ll ans = 1;
       while(b) {
           if(b & 1) ans = (ans * a) % mod;
           b >>= 1; a = (a * a) % mod;
       }
       return ans;
   }
   
   void init() {
       inv[1] = 1;
       for(int i = 2; i < maxn; i++)
           inv[i] = (mod - mod / i) * inv[mod % i] % mod;
       F[0] = Finv[0] = 1;
       for(int i = 1; i < maxn; i++) {
           F[i] = F[i - 1] * i % mod;
           Finv[i] = Finv[i - 1] * inv[i] % mod;
       }
   }
   
   ll C(ll n, ll m) {
       if(n < 0 || m < 0 || m > n) return 0;
       return F[n] * Finv[m] % mod * Finv[n - m] % mod;
   }
   
   int main() {
   	init();
   	int t, kase = 0;
       cin >> t;
       while (t--) {
           cin >> n >> m >> k;
           ans = C(m, k) * k % mod * qpow(k - 1, n - 1) % mod;
           ll sign = 1;
           for(ll i = 2; i < k; i++) {
           	ans = (ans + C(k, i) * i % mod * qpow(i - 1, n - 1) % mod * sign + mod) % mod;
           	sign = -sign;
           }
           cout << "Case #" << ++kase << ": " << ans << endl;
       }
       return 0;
   }
   ```