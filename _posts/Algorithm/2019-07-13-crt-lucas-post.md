---
layout: post
title:  "Algorithm： CRT、EX-CRT & Lucas、Ex-Lucas"
date:   2019-07-13
excerpt: "NULL"
tag:
- Algorithm
---

# 中国剩余定理

中国剩余定理，Chinese Remainder Theorem，又称孙子定理，给出了一元线性同余方程组的有解判定条件，并用构造法给出了通解的具体形式。
$$
\begin{aligned}
&现在有方程组：\\
&(S):\begin{cases}
x\equiv a_1(mod\space m_1)\\
x\equiv a_2(mod\space m_2)\\
\space\space\space\space. \\
\space\space\space\space. \\
\space\space\space\space. \\
x\equiv a_n(mod\space m_n)\\
\end{cases}\\
&中国剩余定理指出：若\forall i,j，1\le i<j\le n，i,j\in Z，都有gcd(m_i,m_j)=1，\\
&则对任意整数：a_1,a_2,...,a_n，方程组(S)均有解，且通解可以用如下方法构造。\\
&设M=\prod_{i=1}^n m_i，并设M_i={M\over m_i}，\forall i\in\{1,2,...,n\}，\\
&设t_i=M_i^{-1}(mod\space m_i)，则方程通解为：x=kM+\sum_{i=1}^n a_it_iM_i，k\in Z。\\
\end{aligned}
$$
下面给出解的正确性证明：
$$
\begin{aligned}
&∵对\forall i,j，1\le i<j\le n，i,j\in Z，gcd(m_i,m_j)=1∴gcd(m_i,M_i)=1 \\
&说明\exists t_i，使得t_iM_i\equiv1(mod\space m_i)\\
&又a_it_iM_i\equiv a_i\cdot1\equiv a_i(mod\space m_i)，且对\forall i,j\in\{1,2,...,n\}，i\ne j，a_it_iM_i\equiv0(mod\space m_j)\\
&∴对\forall i\in\{1,2,..,n\}，x_0=\sum_{j=1}^n a_jt_jM_j=a_it_iM_i+\sum_{j=1,j\ne i}^n a_jt_jM_j\equiv a_it_iM_i+\sum_{j=1,j\ne i}^n0(mod\space m_i)\\
&说明x_0是方程组的一个解\\
&假设x_1,x_2都是方程组的解，那么\forall i\in \{1,2,..,n\}，x_1-x_2\equiv0(mod\space m_i)\\
&又对\forall i,j，1\le i<j\le n，i,j\in Z，gcd(m_i,m_j)=1，∴(x_1-x_2)\%M=(x_1-x_2)\%(\prod_{i=1}^n m_i)=0\\
&∴\exists k\in Z，x_1-x_2=kM，∴方程通解为：x=kM+\sum_{i=1}^n a_it_iM_i，k\in Z
\end{aligned}
$$
<!--例题：hdu 1370-->

# 扩展中国剩余定理

在一般情况下，要求任两个数互质这个条件太苛刻了，CRT派不上用场，我们需要一个更具普遍性的结论，这就是EX-CRT。虽然是称为EX-CRT，但这个定理并没有直接用到CRT的结论。
$$
\begin{aligned}
&我们单独考虑方程组的前两个式子组成的同余方程组S'：\\
&\begin{cases}
x\equiv a_1(mod\space m_1)\\
x\equiv a_2(mod\space m_2)\\
\end{cases}\\
&我们设整数K_1,K_2满足x=K_1m_1+a_1,x=K_2m_2+a_2，那么有K_1m_1+a_1=K_2m_2+a_2，\\
&即m_1K_1-m_2K_2=a_2-a_1，根据EXGCD，方程在gcd(m_1,m_2)|(a_2-a_1)时有解\\
&把由EXGCD求得的特解记为k_1,k_2，设S’的一个解为x_0，那么有：\\
&\begin{cases}
x_0=a_1+k_1\times{a_2-a_1\over gcd(m_1,m_2)}\times m_1\\
x_0=a_2+k_2\times{a_2-a_1\over gcd(m_1,m_2)}\times m_2\\
\end{cases}\\
&这样我们就得到了S'的一个解x_0，怎么求出它的通解呢？\\
\end{aligned}
$$

------

$$
\begin{aligned}
&假设S'存在两个解x_1,x_2(x_1<x_2)，由于x_1,x_2均满足S'，则：\\
&\exists \sigma_1,\sigma_2\in Z，满足：
\begin{cases}
x_2=x_1+\sigma_1\times m_1\\
x_2=x_1+\sigma_2\times m_2\\
\end{cases}\\
&显然\sigma_1\times m_1=\sigma_2\times m_2，设d=gcd(m_1,m_2),n_1={m_1\over d},n_2={m_2\over d}\\
&那么上式\Leftrightarrow \sigma_1\times n_1d=\sigma_2\times n_2d\Leftrightarrow \sigma_1\times n_1=\sigma_2\times n_2\space∴n_1|(\sigma_2\times n_2)\\
&又由定义可知，gcd(n_1,n_2)=1\space ∴n_1|\sigma_2，也即(n_1n_2)|(\sigma_2\times n_2)\rightarrow (n_1\times n_2d)|(\sigma_2\times n_2d)\\
&\rightarrow (n_1\times n_2d)|(\sigma_2\times m_2)\rightarrow (n_1\times n_2d)|(x_2-x_1)(由x_2=x_1+\sigma_2\times m_2可得)\\
&n_1\times n_2d={n_1d\times n_2d\over d}={m_1m_2\over gcd(m_1,m_2)}=lcm(m_1,m_2)\\
&由此我们得到了S’任两解之间的关系：lcm(m_1,m_2)|(x_2-x_1)\\
\end{aligned}
$$

------

$$
\begin{aligned}
&由解的关系有x_2=x_1+k\cdot lcm(m_1,m_2)，k\in Z，结合之前得到的特解x_0，可以得出S'的通解X：\\
&X=x_0+k\cdot lcm(m_1,m_2)，k\in Z\\
&上式又可以写成X\equiv x_0(mod\space lcm(m_1,m_2))，这样，我们就把S'转化为了一条同余式\\
&对于n条同余式，只需逐条合并即可。
\end{aligned}
$$

代码如下：

```c++
typedef long long ll;
const int maxn = 111;

// m为模数组，a为余数数组，0~n-1
ll m[maxn], a[maxn];

ll exgcd(ll a, ll b, ll &x, ll &y) {
    if (b == 0) {
        x = 1; y = 0;
        return a;
    }
    ll ans = exgcd(b, a % b, y, x);
    y -= a / b * x;
    return ans;
}

ll excrt() {
    ll lcm = m[0], last_a = a[0];
    for(int i = 1; i < n; i++) {
        ll lcm_a = ((a[i] - last_a) % m[i] + m[i]) % m[i];
        ll k = lcm, x, y;
        ll gcd = exgcd(lcm, m[i], x, y);
        ll mod = m[i] / gcd;
        x = (x * lcm_a / gcd % mod + mod) % mod;
        lcm = lcm / gcd * m[i], last_a = (last_a + k * x) % lcm;
    }
    return (last_a % lcm + lcm) % lcm;
}
```

<!--例题：洛谷 P4777-->

# 卢卡斯定理

卢卡斯定理是关于组合数和同余的定理，它表明当p为素数时：
$$
\begin{aligned}
&C_n^m=\prod_{i=0}^k C_{n_i}^{m_i}(mod\space p)\\
&其中，m=m_kp^k+m_{k-1}p^{k-1}+...+m_1p+m_0\\
&n=n_kp^k+n_{k-1}p^{k-1}+...+n_1p+n_0\\
&即m_i,n_i为m,n的p进制展开中对应次数为i的系数\\
\end{aligned}
$$
因为当m>n时，二项式系数为0，那么二项式系数即组合数能被p整除等价于在p进制下，存在某一位m的数值大于对应的n的数值。

基于母函数可以简单证明这个定理。
$$
\begin{aligned}
&∵无论p是质数还是合数，对\forall i\in N,0<i<p，都有C_p^i={p!\over i!(p-i)!}\\
&∴在母函数中，对于素数p，(1+x)^p\equiv1+x^p(mod\space p)\\
&运用数学归纳法可以得到，对\forall i\in N，(1+x)^{p^i} \equiv1+x^{p^i} (mod\space p)\\
&又对\forall t\in N以及素数p，把t表示为p进制数有：t=\sum_{i=0}^kt_ip^i\\
&那么，把m,n都用p进制表示，有\\
&\space\space\space\space\sum_{m=0}^n\left(C_n^m\cdot x^m\right)\\
&=(1+x)^n=\prod_{i=0}^k\left[(1+x)^{p^i}\right]^{m_i}\\
&\equiv\prod_{i=0}^k\left(1+x^{p^i}\right)^{m_i}
=\prod_{i=0}^k\left(\sum_{m_i=0}^{n_i}C_{n_i}^{m_i}\cdot x^{m_ip^i}\right)\\
&=\prod_{i=0}^k\left(\sum_{m_i=0}^{p-1}C_{n_i}^{m_i}\cdot x^{m_ip^i}\right)
=\sum_{m=0}^n\left[\left(\prod_{i=0}^kC_{n_i}^{m_i}\right)\cdot x^m\right] (mod\space p)
\end{aligned}
$$
代码如下：

```c++
typedef long long ll;
const int mod = 1e9 + 7;
const int maxn = 1e5 + 100;

void init() {
	F[0] = 1;
	for(int i = 2; i < maxn; i++)
		F[i] = i * F[i - 1] % mod;
}

ll qpow(ll a, ll b) {
	ll ans = 1;
	while(b) {
		if(b & 1) ans = ans * a % mod;
		b >>= 1; a = a * a % mod;
	}
	return ans;
}

ll lucas(ll N, ll M) {
	ll ans = 1;
	while(N & M) {
		ll n = N % mod, m = M % mod;
		if(n < m) return 0;
		ans = ans * F[a] % mod * qpow(F[m] * F[n - m] % mod, mod - 2) % mod;
		N /= p; M /= p;
	}
	return ans;
}
```

<!--例题：洛谷 P3807-->

# 扩展卢卡斯定理

卢卡斯定理同样不能处理模数不是素数的情况，这时便需要扩展卢卡斯定理。我们一步步分析如何求解模数不是素数的组合数问题。

代码和推导部分参考了这篇[博客](https://www.cnblogs.com/zyt1253679098/p/9736741.html)。
$$
\begin{aligned}
&首先，我们要解决的问题是求C_n^m\%p，其中p不一定是素数。\\
&对于非素数，我们首先会联想到质因分解后结合CRT解决问题。\\
&假设分解得到t个质数，质数p_i对应的个数为k_i，对p质因分解有p=\prod_{i=1}^tp_i^{k_i}。\\
&显然对\forall i,j，1\le i<j\le t，gcd(p_i^{k_i},p_j^{k_j})=1，假设对\forall i\in[1,t]，我们求出了a_i=C_n^m\%p_i^{k_i}，\\
&那么我们可以得到同余方程组S：\\
&S：\begin{cases}
C_n^m\equiv a_1(mod\space p_1^{k_1})\\
C_n^m\equiv a_2(mod\space p_2^{k_2})\\
\space\space\space\space\space\space\space.\\
\space\space\space\space\space\space\space.\\
\space\space\space\space\space\space\space.\\
C_n^m\equiv a_t(mod\space p_t^{k_t})\\
\end{cases}\\
&这时我们便可以套用CRT解决问题，那么问题便转化为如何求解C_n^m\%p_i^{k_i}。
\end{aligned}
$$

------

$$
\begin{aligned}
&现在我们要求的是C_n^m\%p^k，其中p是素数。\\
&又C_n^m={n!\over m!(n-m)!}，显然需要求出m!和(n-m)!关于模p^k的逆元，\\
&但考虑到这些项中可能包含p（含有p则不互质，逆元不存在），所以需要先提取p，\\
&得到：C_n^m={{n!\over p^{k_n}}\over{m!\over p^{k_m}}\cdot{(n-m)!\over p^{k_{n-m}}}}\times p^{k_n-k_m-k_{n-m}}，这里的阶乘是指提取之后的结果。\\
&这时就可以计算{m!\over p^{k_m}}和{(n-m)!\over p^{k_{n-m}}}关于p^k的逆元了。\\
&这里，为了形式的统一，同时提取了n!中的p。那么，问题又转化为了如何求n!\%p^k。
\end{aligned}
$$

------

$$
\begin{aligned}
&目标：计算n!\%p^k，p为质数。上一步中提到，我们需要先提取p。\\
&提取结果为：n!=p^{\lfloor{n\over p}\rfloor}\times {\lfloor{n\over p}\rfloor}!\times \prod_{i=1,i\%p\ne 0}^ni。\\
&第一部分很好理解，对于每一个p的倍数，都可以提取出一个p，一共有{\lfloor{n\over p}\rfloor}个；\\
&第二部分为p的倍数被提取p之后余下的，是一个阶乘的形式。显然在n!中，\\
&对于p的幂，p的个数不止1个，也就是说第二部分仍然需要提取，这一部分可以递归解决。\\
&第三部分是n!剔除了p的倍数之后余下的。\\
&∵对\forall i<p^k，t\in N，都有i\equiv(i+t\cdot p^k)(mod\space p^k)\\
&∴对\forall t\in N，\prod_{i=1,i\%p\ne 0}^{p^k}i\equiv\prod_{i=1,i\%p\ne 0}^{p^k}(i+t\cdot p^k)(mod\space p^k)\\
&这也就是说，第三部分其实是存在循环节的，\prod_{i=1,i\%p\ne 0}^{p^k}i一共循环了\lfloor{n\over p^k}\rfloor次。\\
&除去循环节的余项长度在p^k之内，直接累乘即可。\\
\end{aligned}
$$

<!--参考链接：https://www.cnblogs.com/zyt1253679098/p/9736741.html -->

完整代码如下：

```c++
typedef long long ll;
const int N = 1e6 + 100;

ll n, m, p;

ll qpow(ll a, ll b, ll mod) {
	ll ans = 1;
	while(b) {
		if(b & 1) ans = ans * a % mod;
		b >>= 1; a = a * a % mod;
	}
	return ans;
}

ll fac(ll n, ll p, ll pk) {
    if (!n) return 1;
    ll ans = 1;
    for (int i = 1; i < pk; i++)
        if (i % p) ans = ans * i % pk;
    ans = qpow(ans, n / pk, pk);
    int npk = n % pk;
    for (int i = 1; i <= npk; i++)
        if (i % p) ans = ans * i % pk;
    return ans * fac(n / p, p, pk) % pk;
}

ll exgcd(ll a, ll b, ll &x, ll &y) {
    if (b == 0) {
        x = 1; y = 0;
        return a;
    }
    ll ans = exgcd(b, a % b, y, x);
    y -= a / b * x;
    return ans;
}

ll inv(ll a, ll p) {
    if(!a) return 0;
    int x, y;
    exgcd(a, p, x, y);
    return (x % p + p) % p;
}

ll C(ll n, ll m, ll p, ll pk) {
    if (n < m) return 0;
    ll fn = fac(n, p, pk),
       fm = fac(m, p, pk),
       fn_m = fac(n - m, p, pk),
       cnt = 0;
    for (ll i = n; i; i /= p)
        cnt += i / p;
    for (ll i = m; i; i /= p)
        cnt -= i / p;
    for (ll i = n - m; i; i /= p)
        cnt -= i / p;
    return fn * inv(fm * fn_m % pk, pk) % pk * qpow(p, cnt, pk) % pk;
}

ll a[N], mod[N]; // a[]是通过卢卡斯分解出来的组合数值，m[]是对应的模数
int cnt; // 质因数的种数

ll CRT() {
    ll M = 1, ans = 0;
    for (int i = 0; i < cnt; i++)
        M *= mod[i];
    for (int i = 0; i < cnt; i++)
        ans = (ans + a[i] * (M / mod[i]) % M * inv(M / mod[i], mod[i]) % M) % M;
    return ans;
}

ll exlucas(ll n, ll m, ll p) {
    ll sqrtp = sqrt(p + 0.5);
    for (int i = 2; p > 1 && i <= sqrtp; i++) {
        ll pk = 1;
        while (p % i == 0)
            p /= i, pk *= i;
        if (pk > 1)
            a[cnt] = C(n, m, i, pk), mod[cnt++] = pk;
    }
    if (p > 1)
        a[cnt] = C(n, m, p, p), mod[cnt++] = p;
    return CRT();
}
```

<!--例题：洛谷 4720-->