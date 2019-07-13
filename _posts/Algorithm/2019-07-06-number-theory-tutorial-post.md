---
layout: post
title:  "GCD、EXGCD、Inverse Element"
date:   2019-07-06
excerpt: "NULL"
tag:
- Algorithm
---

# 数论基础

数论是纯数学的一个研究分支，主要研究整数的性质。初等数论包括整除理论、同余理论、连分数理论。这一篇主要记录的是同余相关的基础知识。

## 取模

取模是一种运算，本质就是带余除法，运算结果就是余数。取模运算结果的符号由被模数（被除数）决定。
$$
7\%4=3;\space7\%(-4)=3;\\
(-7)\%4=-3;\space(-7)\%(-4)=-3
$$

### 取模运算的性质

$$
设a>b>0，有：\\
(a+b)\%c=(a\%c+b\%c)\%c\\
(a-b)\%c=(a\%c-b\%c+c)\%c \\
(a\times b)\%c=(a\%c\times b\%c)\%c
$$

### 例题1

$$
给定两个非负整数a，b和正整数n，计算f(a^b)\%n。\\
其中，f(0)=f(1)=1,且对于\forall i>0，f(i+2)=f(i+1)+f(i)\\
(0\le a,b\le 2^{64},1\le n\le 1000)
$$

找规律，对于mod n来说，最多n²项就会出现重复，找出重复项即可得到循环周期，然后找对应的项即可。

### 例题2：hdu 1212

题意是给一个位数不超过1,000的正整数A和一个大小不超过100,000的正整数B，求A%B。

这里只需要用到上面的两条性质，把A截断，从高到低模拟做除法即可。

```c++
#include <iostream>
string s; int mod, ans;
int main() {
    while(std::cin >> str >> mod) {
        ans = 0;
        for(int i = 0; s[i]; i++) ans = (ans * 10 + (s[i] - '0')) % mod;
        std::cout << ans << std::endl;
    }
    return 0;
}
```

## GCD

GCD即Greatest Common Divisor，最大公约数。最大公约数即能同时整除给定两个整数的最大正整数。特殊地：gcd（a，0）=a。求解最大公约数通常使用辗转相除法。下面是辗转相除法的代码实现：

```c++
typedef long long LL;
LL gcd(LL a, LL b) {
    return b ? gcd(b, a % b) : a;
}
```

辗转相除法得到的余数序列增长速度比斐波那契数列更快，已知斐波那契的增长是指数级别，则辗转相除法的复杂度是对数级别。

辗转相除法的证明：

1.设两个数a、b（a＞b），他们的最大公约数为gcd（a，b），r = a % b，k = （a - a % b）/ b，那么证明辗转相除法，即是要证明：gcd（a，b）=gcd（b，r）。首先我们令c = gcd（a，b），那么肯定存在互质的整数m，n，使得a = mc，b = nc。那么有r = a - kb = mc - knc = (m - kn)c，根据这条式子，c也是r的因数。回过头看，如果能证明m-kn和n互质，那么就可以说明c=gcd（（m-kn）c，nc）=gcd（b，r），所以把问题再次转化为：求证m-kn和n互质。

2.反证法证明m-kn和n互质：假设m-kn和n不互质，用数学语言描述为：假设存在整数x，y，d，其中d＞1，使得m-kn=xd，n=yd。那么有m=kn+xd=kyd+xd=(ky+x)d，从而a=mc=(ky+x)cd，b=nc=ycd，则gcd（a，b）=cd≠c，与前设矛盾。故m-kn和n互质得证，也就证明了gcd（a，b）=gcd（b，r）。

## 唯一分解定理

对于任意大于2的正整数X，它总能写成如下形式：
$$
X=p_1^{a_1}\times p_2^{a_2} \times ......\times p_k^{a_k}，其中p是各不相同的质数 
$$
即质因数分解。且这个分解唯一。

## LCM

LCM即Least Common Multiple，最小公倍数。

根据唯一分解定理，对于给定的a，b：
$$
a=p_1^{a_1}\times p_2^{a_2} \times......\times p_k^{a_k}\\
b=p_1^{b_1}\times p_2^{b_2} \times......\times p_k^{b_k}
$$
那么：
$$
gcd(a,b)=p_1^{min(a_1,b_1)}\times p_2^{min(a_2,b_2)}\times ......p_1^{min(a_k,b_k)}\\
lcm(a,b)=p_1^{max(a_1,b_1)}\times p_2^{max(a_2,b_2)}\times ......p_1^{max(a_k,b_k)}\\
因此，gcd(a,b)\times lcm(a,b)=a\times b
$$

```c++
LL lcm(LL a, LL b) {
    return a / gcd(a, b) * b;
}
```

### 例题：hdu1788

题意是求最小的正整数N，满足除以每一个给定的数Mi，其结果均为Mi-a。作如下转化：
$$
N\%M_i\equiv(M_i-a)\%M_i\Rightarrow (N+a)\%M_i=0（a<M_i<100）
$$
即N是Mi的倍数，那么题意也就是求所有M的最小公倍数，注意数字范围即可。

## 同余

对于两个不同的数a，b，如果有a % p = b % p（p＞1），那我们就称a和b模p同余，记作：
$$
a\equiv b(mod\space p)
$$

### 同余的性质

$$
\begin{aligned}
&1.自反性：a\equiv a(mod\space m)\\
&2.对称性：a\equiv b(mod\space m)，则b\equiv a(mod\space m)\\
&3.传递性：若a\equiv b(mod\space m)，b\equiv c(mod\space m)，则a\equiv c(mod\space m)\\
&4.线性运算：若a\equiv b(mod\space m)，c\equiv d(mod\space m)\\
&\space\space\space则：a\pm c\equiv b\pm d(mod\space m);\space a\times c\equiv b\times d(mod\space m)\\
&5.幂运算：若a\equiv b(mod\space m)，那么a^n\equiv b^n(mod\space m)\\
&6.若ac\equiv bc(mod\space m)，且c≠0，那么a\equiv b(mod\space {m\over gcd(c,m)})\\
&\space\space\space特殊地，若gcd(c,m)=1，则a\equiv b(mod\space m)
\end{aligned}
$$

## EXGCD

贝祖定理（Bezouts Identity）：若设a,b是不全为0的整数，则存在整数x,y，使得ax+by=gcd（a,b），(a,b)代表最大公因数，则设a,b是不全为零的整数，则肯定存在整数x,y，使得ax+by=(a，b)。这个式子称为贝祖等式。

EXtend GCD 即扩展欧几里得算法，它可以用于解关于x，y的贝祖等式。

### EXGCD的可行性

当a=0时，ax+by=gcd（a，b）=gcd（0，b）=b，这时可以解出y=1而x为任意。同理可得b=0时，解得x=1而y为任意。当a，b都不为0时：
$$
\begin{aligned}
&设存在一组解为x_1，y_1，即ax_1+by_1=gcd(a,b)\\
&由欧几里得定理：gcd(a,b)=gcd(b,a\%b)得：\\
&ax_1+by_1=gcd(a,b)=gcd(b,a\%b)\\
&那么,bx_2+(a\%b)y_2=gcd(b,a\%b)=ax_1+by_1\\
&又a\%b=a-a/b\times b\\
&\begin {align}
那么，&ax_1+by_1\\
=&bx_2+(a-a/b\times b)y_2\\
=&bx_2+ay_2-a/b*b*y_2\\
=&ay_2+b(x_2-a/b*y_2)
\end{align}\\
&故可以得到：x_1=y_2,\space y_1=x_2-a/b*y_2
\end{aligned}
$$
a或b为0即迭代求解的出口，迭代过程用代码表示如下：

```c++
typedef long long LL;
// ver 1
// 返回值为gcd（a，b），x和y即求出的特解
LL exgcd(LL a, LL b, LL &x, LL &y) {
    if (b == 0) {
        x = 1; y = 0;
        return a;
    }
    LL ans = exgcd(b, a % b, x, y);
    //这里通过迭代求出了一组解x，y，这组解对应上面推导过程中的x2和y2。
    LL temp = x;
    x = y;
    y = temp - a / b * y;
    return ans;
}

// ver 2
// 在熟悉了推导过程之后，我们可以利用引用的性质得到更为简洁的ver2写法
LL exgcd(LL a, LL b, LL &x, LL &y) {
    if (b == 0) {
        x = 1; y = 0;
        return a;
    }
    LL ans = exgcd(b, a % b, y, x);
    y -= a / b * x;
    return ans;
}
```

### EXGCD的应用

#### 求关于x，y的方程ax+by=c的一组解

这里只需要对贝祖等式稍作变换即可。假设EXGCD求出的方程ax+by=gcd（a，b）的一组解为x1和y1。在方程两边同时乘上一个数m，使得c=m*gcd（a，b），这里也就要求c是gcd（a，b）的倍数，即c%gcd（a，b）=0。这也是方程有解的条件。此时方程为：
$$
ax_1\times m+by_1\times m=gcd(a,b)\times m=c
$$
对于ax+by=gcd（a，b），其参数解为：
$$
x=x_1+{b\over gcd(a,b)}\times t,y=y_1-{a\over gcd(a,b)}\times t,t为参数\\
这里选用{b\over gcd(a,b)}和{a\over gcd(a,b)}是为了保证结果均为整数
$$
那么对比方程ax+by=c，可以得到其特解为：
$$
x_0=x_1\times m=x_1\times {c\over gcd(a,b)}\\
y_0=y_1\times m=y_1\times {c\over gcd(a,b)}
$$
那么其通解为：
$$
X=x_0+{b\over gcd(a,b)}\times t,Y=y_0-{a\over gcd(a,b)}\times t,t为参数\\
这里选用{b\over gcd(a,b)}和{a\over gcd(a,b)}作为系数是为了确保得到均为整数解。
$$
对于任意一个确定的t，都有一组确定的解与之对应，只需要根据需要找出对应的解即可。

例如求满足ax+by=c的X的最小非负整数解，那么在得到X的通解之后只需调整t，调整过程用伪代码表示为：

```c++
S = b / gcd(a, b);
X = (x0 % S + S) % S;
// 由于x0可能是负数，故在模之后还要加上S在取一次模。
// 同理可得Y的最小非负整数解
T = a / gcd(a, b);
Y = (y0 % T + T) % T;
```

完整的exgcd求解方程ax+by=c的X和Y的最小非负整数解得代码如下：

```c++
LL exgcd(LL a, LL b, LL c, LL& x, LL& y) {
    if (b == 0) {
        x = 1; y = 0;
        return a;
    }
    LL ans = exgcd(a, b, c, y, x);
    y -= a / b * x;
    LL S = b / ans, T = a / ans;
    x = (x % S + S) % S;
    y = (y % T + T) % T;
    return ans;
}
```

#### 求关于x的方程ax≡b（mod m）的最小非负整数解 

ax≡b（mod m）即ax%m=b%m，即求ax+my=b%m（取模其实就相当于减去（加上）了若干个模数）。但只有a和m互质时有唯一解，否则无解。转化为ax+my=b（假设b<m）后，套用exgcd即可。

#### 求a关于模数p的乘法逆元

设x是a关于p的乘法逆元，那么有ax≡1（mod p），这是第二个应用的特殊情况，显然也可以通过类似方法求得。

## 逆元

Inverse Element，逆元，推广了加法中的加法逆元和乘法中的倒数。直观地说，它是一个可以取消另一给定元素运算的元素。a关于模p的逆元存在的条件是gcd（a，p）=1。
$$
在模p意义下，设A的逆元为A^{-1}，那么有A\times A^{-1}\equiv 1(mod\space p)
$$
为什么需要逆元呢？
$$
在模意义下，{A\over B}\%p = {A\%p\over B\%p}\%p并不成立，\\
那么设B在模p意义下的逆元表示为B^{-1}，根据逆元的定义，有{A\over B}=A\times B^{-1}(mod \space p)。
$$
这里，我们把除法转化为乘法，就可以运用取模运算的性质：(a * b) % c = (a % c * b % c) % c，优化算法。

### 逆元的求法

#### EXGCD求法

给定模数m，求a的逆元相当于求解关于x的方程ax≡1(mod m)。这个方程可以转化为ax-my=1 。用EXGCD可以求得一组x，y和gcd。检查gcd是否为1（因为EXGCD是把问题转化为求解方程ax-my=gcd（a，m），显然只有gcd（a，m）=1时才能转化），gcd不为1则说明逆元不存在。在能够成功求解的情况下，把解调整x到0~m-1的范围中即可。

#### 费马小定理

在模p为质数的情况下，有费马小定理：
$$
a^{p-1}\equiv 1(mod\space p)
$$
那么:
$$
a\times a^{p-2}\equiv 1(mod\space p)\\
故a的逆元a^{-1}=a^{p-2}
$$
然后用快速幂求出逆元即可。

拓展：快速幂

```c++
// a是底数，b是指数
LL pow(LL a, LL b, LL mod) {
    LL ans = 1;
    while(b) {
        if(b&1) ans = ans * a % mod;
        b >>= 1, a = a * a % mod;
    }
    return ans;
}
```

拓展：慢速乘，防止快速幂过程中乘法运算爆long long，只需把快速幂中的乘法替换为mul（）即可，一般情况用不上。

```c++
// a为因数1，b为因数2
LL mul(LL a, LL b, LL mod) {
    LL ans = 0;
    while(b) {
        if(b&1) ans = (ans + a) % mod;
        b >>= 1, a = (a + a) % mod;
    }
    return ans;
}
```

#### 欧拉定理

费马小定理其实是欧拉定理的特殊情况。在模数p不为质数时，有：
$$
a^{\phi(p)}\equiv 1(其中\phi()是欧拉函数)
$$
同理可以得到：
$$
a^{-1}\equiv a^{\phi(p)-1}
$$

#### 线性逆元表

有时我们需要快速得出1~p-1的所有逆元，这个时候我们需要一种O（n）的方法，这就是线性逆元表。

首先，1关于任意模p的逆元的逆元都是1。设p = k * i + r（r < i ，1 < i < p），那么有：
$$
k\times i + r \equiv0(mod\space p)
$$
利用逆元性质，两边同时乘上i的逆元以及r的逆元，得到：
$$
k\times r^{-1}+i^{-1}\equiv 0(mod\space p)
$$
移项得：
$$
i^{-1}\equiv-k \times r^{-1}\equiv -⌊{p\over i}⌋\times (p\%i)^{-1}(mod\space p)
$$
显然p%i是小于i的，那么我们就可以利用之前的结果在O（1）时间算出单独的逆元。

用代码表示即：

```c++
// inv[i]对应i的逆元
LL inv[maxn];
void CalInv() {
	// 0没有逆元，故不初始化
    inv[1] = 1;
    for (int i = 2; i < maxn; i++)
        inv[i] = (mod - mod / i) * inv[mod % i] % mod;
}
```

利用
$$
(a\times c)\% mod = (a\%mod\times c\%mod)\%mod；\\
由a\times a^{-1}\equiv1(mod \space p)得，\\
inv_{fac(i)}\equiv inv_{fac(i)}\times (i+1)^{-1}\times (i+1)\equiv inv_{fac(i+1)}\times (i+1)(mod\space p)
$$
我们还可以在线性时间求出1~min(n，p)的阶乘的逆元，代码如下：

```c++
LL fac[maxn], inv[maxn];
void CalFacInv() {
    // 0的阶乘是1
    fac[0] = fac[1] = 1;
    for (int i = 2; i < maxn; i++)
        fac[i] = fac[i - 1] * i % mod;
    inv[maxn - 1] = QPow(fac[maxn - 1], mod - 2);
    // 注意边界是≥
    for (int i = maxn - 2; i >= 0; i--)
        inv[i] = inv[i + 1] * (i + 1) % mod;
}
```

### 例题：hdu1576

题意即把除法转化为逆元乘法。3种方法的代码：

```c++
#include<iostream>
#include<algorithm>
using namespace std;

typedef long long LL;
LL n, B;
const LL mod = 9973;

LL exgcd(LL a, LL b, LL &x, LL &y) {
    if (b == 0) {
        x = 1, y = 0;
        return a;
    }
    LL ans = exgcd(b, a % b, y, x);
    y -= a/b*x;
    return ans;
}

LL QPow(LL x, LL n)
{
    LL res = 1;
    while(n)
    {
        if(n & 1) res = res * x % mod;
        x = x * x % mod;
        n >>= 1;
    }
    return res;
}

LL inv[10000];
void CalInv() {
    inv[1] = 1;
    for (LL i = 2; i < 10000; i++) {
        inv[i] = (mod * mod - mod / i * inv[mod % i]) % mod;
    }
}


int main() {
    CalInv();
    int t;
    cin >> t;
    while (t--) {
        cin >> n >> B;
        // exgcd
        // LL x, y;
        // exgcd(B, mod, x, y);
        // cout << (x + mod) * n % mod << endl;
        
        // QPow
        // cout << QPow(B, mod - 2) * n % mod << endl;
        
        // 逆元打表
        cout << inv[B % mod] * n % mod << endl;
    }
    return 0;
}

```

