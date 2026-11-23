## 声明：

`这个文件没有任何模板，而是有关数学的公式定理以及小技巧、结论的合集`

记号约定：$C(n,m)$ 表示从 $n$ 个里选 $m$ 个（即 $\binom{n}{m}$）；$[P]$ 表示 $P$ 成立时为 $1$，否则为 $0$。

---

## 一、组合数学

### 1. 勒让德公式（Legendre's formula）

$v_p(n!)$ 表示 $n!$ 中质因子 $p$ 的指数：

$$v_p(n!) = \sum_{k \ge 1} \left\lfloor \frac{n}{p^k} \right\rfloor = \frac{n - s_p(n)}{p-1}$$

其中 $s_p(n)$ 是 $n$ 在 $p$ 进制下的数位和。

```cpp
int legendre(int n, int p) {
    int cnt = 0;
    while (n) n /= p, cnt += n;
    return cnt;
}
```

用途：exLucas、库默尔定理、$n!$ 末尾 $0$ 的个数（即 $\min(v_2(n!), v_5(n!))$）。

### 2. 库默尔定理（Kummer）

$v_p\binom{n}{m}$ 等于 $m$ 与 $n-m$ 在 $p$ 进制下相加的进位次数，也等于 $n$ 减去 $m$ 时的借位次数。

推论：

- $p \nmid \binom{n}{m}$ $\iff$ $m$ 与 $n-m$ 在 $p$ 进制下相加无进位。
- $p = 2$ 时：$\binom{n}{m}$ 是奇数 $\iff (n\ \&\ m) = m$。
- 与 Lucas 定理配合：某一位 $m_i > n_i$ 就会产生一次借位。

### 3. 范德蒙恒等式（Vandermonde）

$$\sum_{k=0}^{r} \binom{n}{k} \binom{m}{r-k} = \binom{n+m}{r}$$

组合意义：$n$ 个红球加 $m$ 个蓝球里选 $r$ 个，枚举选了几个红球。

常见特例 / 变形：

$$\sum_{k} \binom{n}{k}^2 = \binom{2n}{n}$$

$$\sum_{k} k\binom{n}{k}\binom{m}{r-k} = n\binom{n+m-1}{r-1}$$

$$\sum_{k} \binom{n}{k}\binom{m}{k} = \binom{n+m}{n}$$

### 4. 常用组合恒等式

$$\binom{n}{k} = \binom{n}{n-k}$$

$$\binom{n}{k} = \binom{n-1}{k} + \binom{n-1}{k-1} \qquad \text{（帕斯卡）}$$

$$k\binom{n}{k} = n\binom{n-1}{k-1} \qquad \text{（吸收恒等式）}$$

$$\binom{n}{k}\binom{k}{m} = \binom{n}{m}\binom{n-m}{k-m}$$

$$\sum_{k=0}^{n} \binom{n}{k} = 2^n$$

$$\sum_{k=0}^{n} k\binom{n}{k} = n\cdot 2^{n-1}$$

$$\sum_{k=0}^{n} k^2\binom{n}{k} = n(n+1)\cdot 2^{n-2}$$

$$\sum_{k=0}^{n} (-1)^k\binom{n}{k} = [n=0]$$

$$\sum_{k=m}^{n} \binom{k}{m} = \binom{n+1}{m+1} \qquad \text{（上指标求和）}$$

$$\sum_{i=0}^{m} \binom{n+i}{i} = \binom{n+m+1}{m} \qquad \text{（平行求和）}$$

$$\binom{-n}{k} = (-1)^k\binom{n+k-1}{k} \qquad \text{（广义二项式，插板法常用）}$$

二项式定理：

$$(x+y)^n = \sum_{k=0}^{n} \binom{n}{k} x^k y^{n-k}$$

多项式定理：

$$(x_1+\cdots+x_t)^n = \sum_{k_1+\cdots+k_t=n} \frac{n!}{k_1!\cdots k_t!} x_1^{k_1}\cdots x_t^{k_t}$$

### 5. 子序列 / 子集相关结论

设序列长度为 $n$，元素为 $a_1,\dots,a_n$：

| 结论 | 公式 |
| --- | --- |
| 长度为 $k$ 的子序列个数 | $\binom{n}{k}$ |
| 子序列总数（含空） | $2^n$ |
| 非空子序列个数 | $2^n-1$ |
| 所有子序列的长度之和 | $n\cdot 2^{n-1}$ |
| 所有子集的元素和之和 | $2^{n-1}\sum_i a_i$ |
| 长度为 $k$ 且任意两元素不相邻的选取数 | $\binom{n-k+1}{k}$ |

推导：每个位置在所有子序列中一共出现 $2^{n-1}$ 次，所以长度和等于 $n\cdot 2^{n-1}$；元素和同理。

补充：

- 所有子集的异或和之和：对每个二进制位 $b$，若数组中至少有一个元素的第 $b$ 位为 $1$，则恰好一半子集的该位异或为 $1$，贡献 $2^{n-1}\cdot 2^b$，否则贡献 $0$。
- `gcd` 恰好为 $d$ 的子序列计数：先统计 `gcd` 是 $d$ 的倍数的子序列数，再从大到小容斥减掉倍数的答案（CF839）。
- `gcd` 是 $d$ 的倍数的子序列总长度等于 $cnt[d]\cdot 2^{cnt[d]-1}$（每个元素出现 $2^{cnt[d]-1}$ 次）。

### 6. 插板法 / 多重集 / 圆排列

| 形式 | 方案数 |
| --- | --- |
| $x_1+\cdots+x_k=n,\ x_i\ge 1$ | $\binom{n-1}{k-1}$ |
| $x_1+\cdots+x_k=n,\ x_i\ge 0$ | $\binom{n+k-1}{k-1}$ |
| $x_1+\cdots+x_k\le n,\ x_i\ge 0$ | $\binom{n+k}{k}$（补一个松弛变量） |
| $x_i\ge l_i$ | 令 $y_i=x_i-l_i$，转化为非负情况 |
| 多重集排列 | $\dfrac{n!}{c_1!c_2!\cdots c_t!}$ |
| 从 $t$ 类元素中可重复选 $n$ 个 | $\binom{n+t-1}{t-1}$ |
| 圆排列 | $(n-1)!$ |
| 项链（可翻转） | $(n-1)!/2$（$n\ge 3$） |

### 7. 二项式反演

第一形式：

$$f(n) = \sum_{i=0}^{n} \binom{n}{i} g(i) \iff g(n) = \sum_{i=0}^{n} (-1)^{n-i}\binom{n}{i} f(i)$$

第二形式（“至少”与“恰好”）：

$$f(k) = \sum_{i=k}^{n} \binom{i}{k} g(i) \iff g(k) = \sum_{i=k}^{n} (-1)^{i-k}\binom{i}{k} f(i)$$

记忆：$f$ 是“至少 / 钦定”，$g$ 是“恰好”。

### 8. 子集反演 / min-max 容斥

$$f(S) = \sum_{T\subseteq S} g(T) \iff g(S) = \sum_{T\subseteq S} (-1)^{|S|-|T|} f(T)$$

min-max 容斥：

$$\max(S) = \sum_{\emptyset\ne T\subseteq S} (-1)^{|T|+1}\min(T)$$

设 $\max(S_{\ge k})$ 表示 $S$ 中第 $k$ 大的元素，则

$$\max(S_{\ge k}) = \sum_{\emptyset\ne T\subseteq S,\ |T|\ge k} (-1)^{|T|-k}\binom{|T|-1}{k-1}\min(T)$$

期望形式（期望的线性性，无需独立性）：

$$\mathbb{E}[\max(S)] = \sum_{\emptyset\ne T\subseteq S} (-1)^{|T|+1}\mathbb{E}[\min(T)]$$

常用来把“最后一个被覆盖”转化为“任意一个集合覆盖的期望”。

### 9. 容斥原理

$$\left|\bigcup_{i=1}^{n} A_i\right| = \sum_i |A_i| - \sum_{i<j}|A_i\cap A_j| + \sum_{i<j<k}|A_i\cap A_j\cap A_k| - \cdots + (-1)^{n+1}\left|\bigcap_{i=1}^{n} A_i\right|$$

写成子集枚举：

$$\left|\bigcup_i A_i\right| = \sum_{\emptyset\ne S\subseteq\{1,\dots,n\}} (-1)^{|S|+1}\left|\bigcap_{i\in S} A_i\right|$$

常用套路：

- 答案 = 总方案数 - 至少一个不合法的方案数。
- 枚举 `mask`，`mask` 中为 $1$ 的位表示“钦定这一维不合法”，用组合数 / DP 直接算剩余部分。
- `gcd` / 子序列问题：从大到小枚举 $d$，减掉 $2d,3d,\dots$ 的答案。

### 10. 斯特林数（Stirling）

第二类斯特林数 $S(n,k)$：$n$ 个不同元素分成 $k$ 个非空无序集合的方案数。

$$S(n,k) = kS(n-1,k) + S(n-1,k-1), \qquad S(0,0)=1$$

$$S(n,k) = \frac{1}{k!}\sum_{i=0}^{k} (-1)^i\binom{k}{i}(k-i)^n$$

$$\sum_{k=0}^{n} S(n,k) = B_n \qquad \text{（贝尔数）}$$

$$x^n = \sum_{k=0}^{n} S(n,k)\,x^{\underline{k}} \qquad \text{（下降幂展开）}$$

$$B_{n+1} = \sum_{k=0}^{n}\binom{n}{k}B_k$$

第一类斯特林数（无符号）$c(n,k)$：$n$ 个元素排成 $k$ 个环的方案数。

$$c(n,k) = (n-1)c(n-1,k) + c(n-1,k-1)$$

$$x^{\overline{n}} = \sum_{k} c(n,k)\,x^k$$

有符号第一类：$s(n,k) = (-1)^{n-k}c(n,k)$。

欧拉数 $A(n,k)$：长度为 $n$ 的排列中恰好有 $k$ 个上升的方案数。

$$A(n,k) = (n-k)A(n-1,k-1) + (k+1)A(n-1,k)$$

### 11. 常见数列（详见对应文件）

| 数列 | 通项 / 递推 | 前几项 |
| --- | --- | --- |
| Catalan | $\mathrm{Cat}_n=\dfrac{1}{n+1}\binom{2n}{n}=\binom{2n}{n}-\binom{2n}{n-1}$；$\mathrm{Cat}_n=\sum_{i=0}^{n-1}\mathrm{Cat}_i\mathrm{Cat}_{n-1-i}$ | 1, 1, 2, 5, 14, 42, 132 |
| 错排 | $D_n=(n-1)(D_{n-1}+D_{n-2})$；$D_n=n!\sum_{i=0}^{n}\dfrac{(-1)^i}{i!}\approx\dfrac{n!}{e}$ | 1, 0, 1, 2, 9, 44, 265 |
| Bell | $B_{n+1}=\sum_{k=0}^{n}\binom{n}{k}B_k$ | 1, 1, 2, 5, 15, 52, 203 |
| Fibonacci | $F_n=F_{n-1}+F_{n-2}$ | 1, 1, 2, 3, 5, 8, 13 |
| 分拆数 $p(n)$ | $\prod_{i\ge1}\dfrac{1}{1-x^i}$ 的系数 | 1, 1, 2, 3, 5, 7, 11 |

五边形数定理（划分数递推）：

$$p(n) = \sum_{k\ge1} (-1)^{k+1}\left[ p\!\left(n-\frac{k(3k-1)}{2}\right) + p\!\left(n-\frac{k(3k+1)}{2}\right) \right]$$

Catalan 的常见应用：合法括号序列、出栈序列、二叉树形态、凸多边形三角剖分、不越过对角线的网格路径。详见 `catalan.md`；阶乘逆元、Lucas、错排代码见 `comb.md`。

### 12. 生成函数基础

$$\frac{1}{1-x} = 1+x+x^2+\cdots$$

$$\frac{1}{(1-x)^k} = \sum_{n\ge0}\binom{n+k-1}{k-1}x^n$$

$$(1+x)^n = \sum_{k}\binom{n}{k}x^k$$

$$\frac{x}{1-x-x^2} = \sum_{n\ge0}F_nx^n \qquad \text{（Fibonacci）}$$

卷积对应多项式乘法：

$$c_n = \sum_{i+j=n} a_i b_j$$

用生成函数计数时，先写出“每个元素可选 / 不可选”的因子再相乘，最后取对应次项系数。

---

## 二、数论

### 1. gcd / lcm

$$\gcd(a,b)\cdot\operatorname{lcm}(a,b) = ab$$

$$\gcd(a,b) = \gcd(b, a\bmod b) = \gcd(a-b,b)$$

$$\gcd(ka,kb) = k\gcd(a,b)$$

$$\operatorname{lcm}(a,b) = \frac{a}{\gcd(a,b)}\cdot b \qquad \text{（先除后乘防溢出）}$$

$$\gcd(0,a)=a$$

$$\gcd(a^m-1,\,a^n-1) = a^{\gcd(m,n)}-1$$

$$\gcd(F_m,F_n) = F_{\gcd(m,n)} \qquad \text{（Fibonacci 数列）}$$

$$\sum_{i=1}^{n}\gcd(i,n) = \sum_{d\mid n}d\,\varphi\!\left(\frac{n}{d}\right)$$

区间 gcd：$\gcd$ 具有可合并性，可用 ST 表加二分求“gcd 相同的区间”。

### 2. 裴蜀定理（Bézout）

$$ax+by=c \text{ 有整数解} \iff \gcd(a,b)\mid c$$

通解：

$$x = x_0 + k\frac{b}{g}, \qquad y = y_0 - k\frac{a}{g}, \qquad g=\gcd(a,b)$$

推广：$\sum_i a_ix_i=c$ 有整数解 $\iff \gcd(a_1,\dots,a_n)\mid c$。

### 3. 逆元

逆元 $a^{-1}$ 存在当且仅当 $\gcd(a,m)=1$，此时 $a\cdot a^{-1}\equiv1\pmod m$。

- $m$ 为质数：$a^{-1}\equiv a^{m-2}\pmod m$（费马小定理）。
- 任意 $\gcd(a,m)=1$：用 `exgcd` 解 $ax+my=1$。
- 任意 $\gcd(a,m)=1$：$a^{-1}\equiv a^{\varphi(m)-1}\pmod m$（欧拉定理）。
- 线性递推（$m$ 为质数，$i<m$）：

$$\operatorname{inv}[i] = \left(m-\left\lfloor\frac{m}{i}\right\rfloor\right)\operatorname{inv}[m\bmod i]\bmod m$$

- 批量求逆元：先求前缀积，再求前缀积的逆元，倒推。
- 不是所有除法都能取模：$a/b\bmod m$ 必须先把 $b$ 换成逆元，且 $\gcd(b,m)=1$。
- 模数不是质数时不能直接用费马小定理。

详见 `inverse.md`。

### 4. 欧拉函数 $\varphi$

$$\varphi(n) = n\prod_{p\mid n}\left(1-\frac{1}{p}\right)$$

$$\varphi(p^k) = p^k-p^{k-1}, \qquad \varphi(1)=1$$

$$\sum_{d\mid n}\varphi(d) = n$$

$$\varphi(n) = \sum_{d\mid n}\mu(d)\frac{n}{d}$$

$$\gcd(a,b)=1 \Rightarrow \varphi(ab)=\varphi(a)\varphi(b)$$

当 $n>2$ 时，$\varphi(n)$ 为偶数。

线性筛递推：

$$p\nmid i:\quad \varphi(ip)=\varphi(i)(p-1)$$

$$p\mid i:\quad \varphi(ip)=\varphi(i)\,p$$

### 5. 费马小定理 / 欧拉定理 / 扩展欧拉

费马小定理（$p$ 为质数，$\gcd(a,p)=1$）：

$$a^{p-1}\equiv1\pmod p$$

欧拉定理（$\gcd(a,m)=1$）：

$$a^{\varphi(m)}\equiv1\pmod m$$

扩展欧拉定理（$b\ge\varphi(m)$）：

$$a^b\equiv a^{\,b\bmod\varphi(m)+\varphi(m)}\pmod m$$

降幂（幂塔）：指数按 $\varphi(m),\varphi(\varphi(m)),\dots$ 递归取模，最多 $O(\log m)$ 层后 $\varphi$ 变成 $1$，此时结果对 $1$ 取模为 $0$。

注意：扩展欧拉定理要求真实指数 $b\ge\varphi(m)$；如果 $b$ 很小，不能直接加 $\varphi(m)$。详见 `Euler.md`。

### 6. 威尔逊定理（Wilson）

$$p \text{ 为质数} \Rightarrow (p-1)!\equiv-1\pmod p$$

反过来：$n>1$ 且 $(n-1)!\equiv-1\pmod n$ $\Rightarrow$ $n$ 为质数。

用途：当 $n$ 接近 $p$ 时求 $n!\bmod p$：

$$n!\equiv -1\cdot\left[\prod_{i=n+1}^{p-1}i\right]^{-1}\pmod p$$

### 7. 阶、原根、离散对数

$$\operatorname{ord}_m(a) \mid \varphi(m)$$

$$a^k\equiv1\pmod m \iff \operatorname{ord}_m(a)\mid k$$

原根存在条件：$m=1,2,4,p^k,2p^k$（$p$ 为奇质数）。原根个数为 $\varphi(\varphi(m))$。

求原根：枚举 $g$，对 $\varphi(m)$ 的每个质因子 $q$ 检查 $g^{\varphi(m)/q}\not\equiv1\pmod m$。

BSGS（$a^x\equiv b\pmod p$，$\gcd(a,p)=1$）：

$$m=\lceil\sqrt p\rceil, \qquad x=im-j\ (0\le j<m), \qquad a^{im}\equiv b\,a^j\pmod p$$

先枚举 $j$ 把 $b\,a^j$ 存进哈希表，再枚举 $i$ 查表，复杂度 $O(\sqrt p)$。

$\gcd(a,p)\ne1$ 时用 exBSGS：不断除以 $\gcd(a,p)$，直到互质，记录额外次数。

### 8. 积性函数

积性：$\gcd(a,b)=1\Rightarrow f(ab)=f(a)f(b)$；完全积性去掉互质条件。

常见数论函数：

| 函数 | 定义 |
| --- | --- |
| $\varepsilon(n)=[n=1]$ | 单位函数 |
| $1(n)=1$ | 常函数 |
| $\operatorname{id}(n)=n$ | 恒等函数 |
| $\mu(n)$ | 莫比乌斯函数 |
| $\varphi(n)$ | 欧拉函数 |
| $d(n)=\sum_{d\mid n}1$ | 约数个数 |
| $\sigma(n)=\sum_{d\mid n}d$ | 约数和 |

若 $n=\prod_i p_i^{a_i}$：

$$d(n)=\prod_i(a_i+1)$$

$$\sigma(n)=\prod_i\frac{p_i^{a_i+1}-1}{p_i-1}$$

Dirichlet 卷积：

$$(f*g)(n)=\sum_{d\mid n}f(d)\,g\!\left(\frac{n}{d}\right)$$

$$\varphi=\mu*\operatorname{id}, \qquad d=1*1, \qquad \sigma=\operatorname{id}*1, \qquad \varepsilon=\mu*1$$

杜教筛：设 $S(n)=\sum_{i=1}^{n}f(i)$，$h=f*g$，$G$ 为 $g$ 的前缀和，则

$$S(n)=\frac{\sum_{i=1}^{n}h(i)-\sum_{l=2}^{n}\bigl(G(r)-G(l-1)\bigr)S\!\left(\left\lfloor\frac{n}{l}\right\rfloor\right)}{g(1)}$$

其中

$$r=\left\lfloor\frac{n}{\lfloor n/l\rfloor}\right\rfloor$$

常用于 $\varphi$（$g=1,\ h=\operatorname{id}$）和 $\mu$（$g=1,\ h=\varepsilon$），配合线性筛预处理可做到 $O(n^{2/3})$。

### 9. 莫比乌斯反演

| 情况 | $\mu(n)$ |
| --- | --- |
| $n=1$ | $1$ |
| $n$ 是 $k$ 个不同质数之积（无平方因子） | $(-1)^k$ |
| $n$ 含平方因子 | $0$ |

$$\sum_{d\mid n}\mu(d) = [n=1]$$

两种反演形式：

$$f(n)=\sum_{d\mid n}g(d) \iff g(n)=\sum_{d\mid n}\mu(d)\,f\!\left(\frac{n}{d}\right)$$

$$f(n)=\sum_{n\mid d}g(d) \iff g(n)=\sum_{n\mid d}\mu\!\left(\frac{d}{n}\right)f(d)$$

最重要的应用：

$$[\gcd(i,j)=1] = \sum_{d\mid\gcd(i,j)}\mu(d)$$

$$\sum_{i=1}^{n}\sum_{j=1}^{m}[\gcd(i,j)=1] = \sum_{d=1}^{\min(n,m)}\mu(d)\left\lfloor\frac{n}{d}\right\rfloor\left\lfloor\frac{m}{d}\right\rfloor$$

配合 $\mu$ 的前缀和与数论分块可做到单次询问 $O(\sqrt n)$。

### 10. 数论分块（整除分块）

$\left\lfloor n/i\right\rfloor$ 的取值只有 $O(\sqrt n)$ 种：

```cpp
for (int l = 1, r; l <= n; l = r + 1) {
    r = n / (n / l);              // 这一块内 floor(n/i) 都等于 n / l
    // ...
}
```

常见公式：

$$\sum_{i=1}^{n}\left\lfloor\frac{n}{i}\right\rfloor = 2\sum_{i=1}^{\lfloor\sqrt n\rfloor}\left\lfloor\frac{n}{i}\right\rfloor - \lfloor\sqrt n\rfloor^2$$

$$\sum_{i=1}^{n}\left\lfloor\frac{n}{i}\right\rfloor = \sum_{i=1}^{n}d(i) \qquad \text{（约数个数前缀和）}$$

区间端点的另一种写法：满足 $\lfloor n/l\rfloor=v$ 的最大 $r=\lfloor n/v\rfloor$。

### 11. 质数与分解

$$\pi(n)\approx\frac{n}{\ln n}, \qquad p_n\approx n\ln n$$

其中 $\pi(n)$ 是 $n$ 以内的质数个数，$p_n$ 是第 $n$ 个质数。

试除分解：

```cpp
for (int i = 2; 1LL * i * i <= x; ++i)
    if (x % i == 0) {
        while (x % i == 0) x /= i;
        // i 是质因子
    }
if (x > 1) // x 本身是质因子
```

Miller-Rabin（64 位整数确定性底数）：$2,3,5,7,11,13,17,19,23,29,31,37$，或 $2,325,9375,28178,450775,9780504,1795265022$。

Pollard-Rho：期望 $O(n^{1/4})$ 分解一个合数，配合 Miller-Rabin 使用。

约数个数最大值参考：

| 范围 | 最大约数个数 |
| --- | --- |
| $n\le10^9$ | $1344$ |
| $n\le10^{18}$ | $103680$ |

求反素数 / 最大约数个数：DFS 枚举质因子指数，指数单调不增。

### 12. 同余方程 / CRT

线性同余方程 $ax\equiv b\pmod m$：

$$\text{有解} \iff \gcd(a,m)\mid b$$

解为 $x\equiv x_0+k\cdot\frac{m}{g}\pmod{\frac{m}{g}}$，其中 $g=\gcd(a,m)$。

CRT（模数两两互质）：

$$M=\prod_i m_i, \qquad M_i=\frac{M}{m_i}, \qquad t_i\equiv M_i^{-1}\pmod{m_i}$$

$$x\equiv\sum_i a_iM_it_i\pmod M$$

EXCRT（模数不一定互质）：合并两个方程时解 $m_1k_1+m_2k_2=r_2-r_1$，用 exgcd；若 $(r_2-r_1)\bmod\gcd(m_1,m_2)\ne0$ 则无解。详见 `CRT.md`。

### 13. 二次剩余

$p$ 为奇质数，$a$ 不是 $p$ 的倍数时：

$$a \text{ 是二次剩余} \iff a^{(p-1)/2}\equiv1\pmod p \qquad \text{（欧拉判别法）}$$

否则 $a^{(p-1)/2}\equiv-1\pmod p$。

- $p\equiv3\pmod4$：$\sqrt a\equiv\pm a^{(p+1)/4}\pmod p$。
- $p\equiv1\pmod4$：用 Cipolla / Tonelli-Shanks。
- 非零二次剩余恰好有 $\frac{p-1}{2}$ 个。

### 14. 其他数论结论

- $n!$ 末尾 $0$ 的个数 $=\min(v_2(n!),v_5(n!))=v_5(n!)$。
- $\gcd(a,m)=1$ 时，$a^x\bmod m$ 的循环节长度整除 $\varphi(m)$。
- 与 $n$ 互质的数对计数：$\sum_{i=1}^{n}[\gcd(i,n)=1]=\varphi(n)$。
- 若 $n$ 是完全平方数，则 $d(n)$ 为奇数；反之 $d(n)$ 为奇数 $\Rightarrow$ $n$ 是完全平方数。

---

## 三、数列与求和

### 1. 基本求和

$$\sum_{i=1}^{n}i=\frac{n(n+1)}{2}$$

$$\sum_{i=1}^{n}i^2=\frac{n(n+1)(2n+1)}{6}$$

$$\sum_{i=1}^{n}i^3=\left[\frac{n(n+1)}{2}\right]^2$$

$$\sum_{i=1}^{n}i^4=\frac{n(n+1)(2n+1)(3n^2+3n-1)}{30}$$

$$\sum_{i=0}^{n}r^i=\frac{r^{n+1}-1}{r-1}\qquad(r\ne1)$$

$$\sum_{i=1}^{n}i\,r^i=\frac{r-(n+1)r^{n+1}+n\,r^{n+2}}{(1-r)^2}\qquad(r\ne1)$$

$$\sum_{i=1}^{n}i\,2^i=(n-1)2^{n+1}+2$$

### 2. 调和级数

$$H_n=\sum_{i=1}^{n}\frac{1}{i}\approx\ln n+\gamma,\qquad \gamma\approx0.5772156649$$

常用：$\sum_{i=1}^{n}\frac{n}{i}=O(n\log n)$（调和级数），这是“枚举约数 / 倍数”的复杂度来源。

### 3. Fibonacci 常用恒等式

$$F_1=F_2=1,\qquad F_n=F_{n-1}+F_{n-2}$$

$$F_{n-1}F_{n+1}-F_n^2=(-1)^n$$

$$\sum_{i=1}^{n}F_i=F_{n+2}-1$$

$$\sum_{i=1}^{n}F_i^2=F_nF_{n+1}$$

$$\gcd(F_m,F_n)=F_{\gcd(m,n)}$$

$$F_{2n}=F_n(2F_{n+1}-F_n)$$

$$F_{2n+1}=F_n^2+F_{n+1}^2$$

$$F_n=\frac{\varphi^n-\psi^n}{\sqrt5},\qquad \varphi=\frac{1+\sqrt5}{2},\quad \psi=\frac{1-\sqrt5}{2}$$

快速倍增（$O(\log n)$ 求 Fibonacci）：

```cpp
pair<long long, long long> fib(long long n) { // 返回 {F_n, F_{n+1}}
    if (n == 0) return {0, 1};
    auto [a, b] = fib(n >> 1);
    long long c = a * (2 * b - a);
    long long d = a * a + b * b;
    return (n & 1) ? make_pair(d, c + d) : make_pair(c, d);
}
```

### 4. 线性递推 / 矩阵快速幂

$$\begin{pmatrix}a_n\\a_{n-1}\end{pmatrix}=\begin{pmatrix}p&q\\1&0\end{pmatrix}\begin{pmatrix}a_{n-1}\\a_{n-2}\end{pmatrix}$$

Fibonacci 的矩阵形式：

$$\begin{pmatrix}F_{n+1}&F_n\\F_n&F_{n-1}\end{pmatrix}=\begin{pmatrix}1&1\\1&0\end{pmatrix}^{n}$$

二阶线性递推 $a_n=p\,a_{n-1}+q\,a_{n-2}$ 的特征方程：

$$x^2-px-q=0$$

两个根 $x_1,x_2$ $\Rightarrow$ $a_n=Ax_1^n+Bx_2^n$。

矩阵快速幂复杂度 $O(k^3\log n)$（$k$ 为矩阵阶数）。

---

## 四、概率与期望

$$\mathbb{E}[X+Y]=\mathbb{E}[X]+\mathbb{E}[Y] \qquad \text{（不要求独立）}$$

$$\mathbb{E}[aX+b]=a\,\mathbb{E}[X]+b$$

$$X,Y\text{ 独立}:\quad \mathbb{E}[XY]=\mathbb{E}[X]\,\mathbb{E}[Y]$$

$$\operatorname{Var}(X)=\mathbb{E}[X^2]-\mathbb{E}[X]^2$$

$$\operatorname{Var}(aX+b)=a^2\operatorname{Var}(X)$$

$$X,Y\text{ 独立}:\quad \operatorname{Var}(X+Y)=\operatorname{Var}(X)+\operatorname{Var}(Y)$$

$$\mathbb{E}[X]=\sum_{i\ge1}\mathbb{P}(X\ge i) \qquad \text{（X 取非负整数）}$$

指示变量法：若 $X=\sum_i I_i$，其中 $I_i$ 是第 $i$ 个事件发生的指示变量，则

$$\mathbb{E}[X]=\sum_i\mathbb{P}(I_i=1)$$

常见分布：

| 分布 | 期望 / 方差 |
| --- | --- |
| 几何分布 $\mathbb{P}(X=k)=(1-p)^{k-1}p$ | $\mathbb{E}[X]=\dfrac{1}{p}$ |
| 二项分布 | $\mathbb{E}[X]=np,\ \operatorname{Var}(X)=np(1-p)$ |
| 连续均匀分布 $U(a,b)$ | $\mathbb{E}[X]=\dfrac{a+b}{2},\ \operatorname{Var}(X)=\dfrac{(b-a)^2}{12}$ |

经典结论：

- $n$ 个不同元素的随机排列期望逆序对：$\dfrac{n(n-1)}{4}$。
- 收集齐 $n$ 种物品的期望次数：$nH_n$。
- 生日碰撞：$n$ 个人中至少两人生日相同的概率约为 $1-\exp\!\left(-\dfrac{n^2}{2N}\right)$，其中 $N$ 为天数；要保证概率至少 $50\%$，需 $n\approx1.177\sqrt N$。

期望 DP 中出现环时，通常列方程后高斯消元，或利用期望线性性避免环。

---

## 五、博弈论

### 1. P / N 态

- P 态（必败）：所有可达状态都是 N 态。
- N 态（必胜）：存在一个可达状态是 P 态。

### 2. Nim 与 SG 定理

Nim：$n$ 堆石子，每次从一堆取任意正数。

$$\text{先手必胜} \iff a_1\oplus a_2\oplus\cdots\oplus a_n\ne0$$

SG 函数：

$$\operatorname{SG}(x)=\operatorname{mex}\{\operatorname{SG}(y)\mid x\to y\}$$

游戏和的 SG 值等于各子游戏 SG 值的异或：

$$\operatorname{SG}(x_1)\oplus\operatorname{SG}(x_2)\oplus\cdots\oplus\operatorname{SG}(x_n)$$

SG 值为 $0$ 是 P 态，非 $0$ 是 N 态。

### 3. 常见博弈结论

Bash 博弈（每次取 $1..m$ 个，取最后一个胜）：

$$\text{先手必败} \iff n\bmod(m+1)=0$$

Wythoff 博弈（两堆，可从一堆取任意个或从两堆取相同个）：

$$\text{必败态：}\ (\lfloor k\varphi\rfloor,\ \lfloor k\varphi^2\rfloor),\qquad \varphi=\frac{1+\sqrt5}{2}$$

判断：设 $a\le b$，必败 $\iff a=\lfloor(b-a)\varphi\rfloor$。

反 Nim（取最后一个者输）：

- 所有堆大小都是 $1$ 且堆数为偶数（异或和为 $0$），先手必胜；
- 或至少有一堆大小 $>1$ 且异或和 $\ne0$，先手必胜。

Fibonacci 博弈（先手不能一次取完，之后每人最多取上一个人取的 $2$ 倍）：先手必败 $\iff n$ 是 Fibonacci 数。

阶梯博弈（阶梯上的石子，只能往下移）：先手必胜 $\iff$ 奇数层（从下往上 $1$-indexed）石子的异或和 $\ne0$。

树上 Green Hackenbush：

$$\operatorname{SG}(u)=\bigoplus_{v\in\operatorname{son}(u)}\bigl(\operatorname{SG}(v)+1\bigr)$$

二分图博弈：先手必胜 $\iff$ 起点属于所有最大匹配（存在一个最大匹配不覆盖起点时先手必败）。

---

## 六、计算几何

### 1. 向量基础

$$\vec a\cdot\vec b=|\vec a||\vec b|\cos\theta=x_1x_2+y_1y_2$$

$$\vec a\times\vec b=|\vec a||\vec b|\sin\theta=x_1y_2-x_2y_1$$

叉积符号表示方向：$\operatorname{cross}(B-A,\ C-A)>0$ $\Rightarrow$ $C$ 在 $AB$ 左侧（逆时针）。

$$\text{共线：}\ \operatorname{cross}(B-A,\ C-A)=0$$

$$\text{三角形面积：}\ S=\frac{1}{2}\left|\operatorname{cross}(B-A,\ C-A)\right|$$

### 2. 点到直线 / 线段

$$\text{点 } P \text{ 到直线 } AB \text{ 的距离：}\ d=\frac{\left|\operatorname{cross}(B-A,\ P-A)\right|}{|B-A|}$$

点到线段距离：投影落在段内取垂距，否则取到两端点的距离。

线段相交：先做包围盒判断，再判断两线段端点分别在另一条线段两侧（叉积异号）。

### 3. 多边形

鞋带公式（任意简单多边形面积）：

$$S=\frac{1}{2}\left|\sum_{i=1}^{n}\left(x_i y_{i+1}-x_{i+1}y_i\right)\right|$$

`Pick` 定理（格点多边形）：

$$S=I+\frac{B}{2}-1$$

其中 $I$ 为内部格点数，$B$ 为边界格点数。

边界格点数：

$$B=\sum_{\text{每条边}}\gcd(|\Delta x|,\ |\Delta y|)$$

### 4. 距离转换

曼哈顿距离 $|x|+|y|$ 与切比雪夫距离 $\max(|x|,|y|)$ 互转：

$$u=x+y,\qquad v=x-y$$

$$|x|+|y|=\max(|u|,|v|)$$

$$x=\frac{u+v}{2},\qquad y=\frac{u-v}{2}$$

即旋转 $45^\circ$：$(x,y)\to(x+y,\ x-y)$。

### 5. 三角形 / 圆

$$\frac{a}{\sin A}=\frac{b}{\sin B}=\frac{c}{\sin C}=2R \qquad \text{（正弦定理）}$$

$$c^2=a^2+b^2-2ab\cos C \qquad \text{（余弦定理）}$$

$$S=\frac{1}{2}ab\sin C=\frac{abc}{4R}=rs,\qquad s=\frac{a+b+c}{2}$$

$$S=\sqrt{s(s-a)(s-b)(s-c)} \qquad \text{（海伦公式）}$$

### 6. 常用算法

- 凸包：Andrew 单调链，$O(n\log n)$。
- 旋转卡壳：凸包直径 / 最近点对。
- 极角排序：先按半平面，再按叉积。

网格路径计数：从 $(0,0)$ 到 $(n,m)$ 只能向右 / 向上，方案数为 $\binom{n+m}{n}$；不能越过某条直线的路径数用反射原理（Catalan 数）。

---

## 七、线性代数与多项式

### 1. 矩阵

$$(AB)_{ij}=\sum_k A_{ik}B_{kj}$$

矩阵乘法不满足交换律；矩阵快速幂复杂度 $O(n^3\log k)$。

行列式：交换两行变号；某行乘 $k$ 行列式乘 $k$；一行加上另一行的倍数不变；三角矩阵行列式等于对角线乘积。

### 2. 高斯消元

- 解线性方程组：$O(n^3)$。
- 行列式：消成上三角后对角线相乘。
- 异或方程组：用 bitset 做高斯消元，自由元个数为 $n-\operatorname{rank}$。
- 异或方程组解的个数：$2^{n-\operatorname{rank}}$。

矩阵树定理：无向图生成树个数等于拉普拉斯矩阵 $L=D-A$ 的任意一个 $(n-1)$ 阶主子式的行列式。

### 3. 线性基（异或）

维护一组基，能表示出原集合所有异或和：

- 插入：从高位到低位，若当前位已有基则异或，否则放入并 `break`。
- 最大值：从高位贪心异或。
- 第 $k$ 小：先化成“每个基的最高位互不相同”的规范形式，再按 $k$ 的二进制取。
- 可表示的不同异或和个数：$2^{\operatorname{rank}}$。
- 判断 $x$ 能否被表示：从高位消到 $0$ 即可。

### 4. 多项式 / 卷积

$$(a*b)_n=\sum_{i+j=n}a_i b_j$$

FFT / NTT：$O(n\log n)$。

常见 NTT 模数：

| 模数 | 分解 | 原根 |
| --- | --- | --- |
| $998244353$ | $119\cdot2^{23}+1$ | $3$ |
| $1004535809$ | $479\cdot2^{21}+1$ | $3$ |
| $469762049$ | $7\cdot2^{26}+1$ | $3$ |

FWT（快速沃尔什变换）用于集合幂级数卷积，对半数组 $A_0,A_1$：

$$\operatorname{FWT}_{\mathrm{XOR}}(A)=\bigl(A_0+A_1,\ A_0-A_1\bigr)$$

$$\operatorname{FWT}_{\mathrm{AND}}(A)=\bigl(A_0+A_1,\ A_1\bigr)$$

$$\operatorname{FWT}_{\mathrm{OR}}(A)=\bigl(A_0,\ A_0+A_1\bigr)$$

逆变换后除以长度（XOR 需要乘对应逆元）。

---

## 八、小技巧 & 易错点

### 1. 二进制技巧

- `lowbit`：$x\ \&\ (-x)$。
- 判断 $2$ 的幂：$x>0$ 且 $x\ \&\ (x-1)=0$。
- 枚举子集：

```cpp
for (int s = mask; s; s = (s-1) & mask) { /* ... */ }
```

- 所有 `mask` 的子集总数：$3^n$。
- 前 $n$ 个数的异或和：

$$f(n)=\begin{cases}n,&n\bmod4=0\\[2pt]1,&n\bmod4=1\\[2pt]n+1,&n\bmod4=2\\[2pt]0,&n\bmod4=3\end{cases}$$

### 2. 取模与溢出

$$(a+b)\bmod m=(a\bmod m+b\bmod m+m)\bmod m$$

$$(a-b)\bmod m=(a\bmod m-b\bmod m+m)\bmod m$$

$(a\cdot b)\bmod m$ 用 `long long`，必要时用 `__int128`。

龟速乘：$a\cdot b\bmod m$ 用二进制拆分，$O(\log b)$，防止 `long long` 溢出。

`__int128` 的读写需要手写：

```cpp
__int128 read() {
    __int128 x = 0; int f = 1; char c = getchar();
    while (c != EOF && !isdigit(c)) { if (c == '-') f = -1; c = getchar(); }
    while (c != EOF && isdigit(c)) { x = x * 10 + c - '0'; c = getchar(); }
    return x * f;
}
```

### 3. 整数除法

C++ 的 `/` 对负数是向零取整，需要手写：

$$\operatorname{floorDiv}(a,b)=\begin{cases}a/b,&a\ge0\\[2pt]-\dfrac{-a+b-1}{b},&a<0\end{cases}$$

$$\operatorname{ceilDiv}(a,b)=\begin{cases}\dfrac{a+b-1}{b},&a\ge0\\[2pt]a/b,&a<0\end{cases}$$

其中除法均为 C++ 整除。

### 4. 复杂度 / 枚举

- 枚举约数：$O(\sqrt n)$，$i$ 与 $n/i$ 成对加入。
- 枚举倍数：$\sum_{i=1}^{n}\frac{n}{i}=O(n\log n)$。
- 枚举所有子集：$O(3^n)$（对每个 `mask` 枚举其子集的总复杂度）。

### 5. 二分 / 三分

- `lower_bound`：第一个 $\ge x$ 的位置。
- `upper_bound`：第一个 $>x$ 的位置。
- 浮点二分：迭代 $100$ 次左右比设定 `eps` 更稳。
- 三分：单峰 / 单谷函数求极值，取

$$m_1=l+\left\lfloor\frac{r-l}{3}\right\rfloor,\qquad m_2=r-\left\lfloor\frac{r-l}{3}\right\rfloor$$

比较 $f(m_1),f(m_2)$ 缩小区间。

### 6. 分数规划

求

$$\max\frac{\sum_i a_ix_i}{\sum_i b_ix_i}$$

二分答案 $\lambda$，判断是否存在方案使 $\sum_i(a_i-\lambda b_i)>0$。

### 7. 常见坑

1. 多测不清空：数组、`vector`、全局变量。
2. `1LL << k`，不要写 `1 << k`。
3. $n$ 可能为 $0/1$，组合数边界 $\binom{n}{0}=1$。
4. 模数不是质数时不能用费马小定理求逆元。
5. 阶乘逆元要求 $n<p$，否则用 Lucas / exLucas。
6. 筛法 `MAXN` 开够：`i * prime[j]` 可能溢出，用 `long long` 判断。
7. 读入大量数据用快读，输出用 `'\n'`。
8. 浮点比较用 `eps`，避免直接用 `==`。
9. `map` / `unordered_map` 在卡常题中慎用。
10. 递归深度过大改迭代或手动开栈。

### 8. 常用模数

| 模数 | 说明 |
| --- | --- |
| $10^9+7$ | 常见答案模数，质数 |
| $998244353$ | NTT 模数，原根 $3$ |
| $10^9+9$ | 质数 |
| $10^6+3$ | 小模数 / 哈希 |
| $19260817$ | 趣味模数，质数 |

### 9. 随机化

```cpp
mt19937 rng(chrono::steady_clock::now().time_since_epoch().count());
int x = rng() % n;
```

用途：哈希防卡、随机化快排、Pollard-Rho、随机增量。

---

## 九、速查清单

### 组合数

| 条件 | 方法 |
| --- | --- |
| $n<p$，$p$ 是质数 | 阶乘 + 逆元 |
| $n$ 很大但 $p$ 很小 | Lucas 定理 |
| $p$ 不是质数 | exLucas / 递推 / 质因子指数 |
| $n$ 很大但 $m$ 很小 | 直接乘 $m$ 项 / 下降幂 |

### 数论

| 问题 | 方法 |
| --- | --- |
| $ax+by=c$ | exgcd + 裴蜀 |
| 同余方程 | exgcd / CRT / EXCRT |
| 幂塔 | 扩展欧拉降幂 |
| 互质计数 | $\varphi$ / $\mu$ + 数论分块 |
| 区间 gcd | ST 表 + 二分 |
| gcd 恰好为 $d$ | 倍数容斥 |

### 几何

| 问题 | 方法 |
| --- | --- |
| 方向 / 面积 | 叉积 |
| 格点 | Pick + gcd |
| 最短路计数 | 组合数 |
| 曼哈顿距离 | 转切比雪夫 |

### 博弈

| 问题 | 方法 |
| --- | --- |
| 取石子 | Nim 异或 / Bash / Wythoff |
| 一般公平组合 | SG 定理 |
| 反着取 | 反 Nim |
