## 先背这张表：

| 场景                      | 方法                                            | 条件                            |
| :------------------------ | :---------------------------------------------- | :------------------------------ |
| 质数模，`n < p`           | 阶乘 + 逆元                                     | `p` 质数，`n` 不超过 `1e6` 左右 |
| 质数模，`n` 很大但 `p` 小 | Lucas 定理                                      | `p` 质数，`n, m` 可达 `1e18`    |
| 非质数模 / 只问倍数       | 帕斯卡递推（n 比较小） / 质因子指数（只问倍数） | `k` 不一定是质数                |

## 三句话：

1. 质模小 n：预处理阶乘和逆阶乘，`C(n,m) = fact[n] * inv_fact[m] * inv_fact[n-m]`。
2. 质小模大 n：Lucas 把 `n, m` 按 `p` 进制拆位，逐位算小组合数再乘起来。
3. 非质模别硬逆元：用帕斯卡递推 `C[i][j] = C[i-1][j-1] + C[i-1][j]`，或者分解质因子算指数。



## 阶乘 + 逆元

适用条件：`质数模，n < p`

```cpp
constexpr int MAXN = 1'005, MOD = 1'000'7;

int inv_fact[MAXN], fact[MAXN];

int a, b, k, m, n;

int fast_pow(int x, int e) {
    int res = 1;
    x %= MOD;
    while (e) {
        if (e & 1) res = res * x % MOD;
        x = x * x % MOD;
        e >>= 1;
    }
    
    return res;
}

int inv(int a) {
    return fast_pow(a, MOD - 2) % MOD;
}

int comb(int m, int n) {
    return fact[k] * inv_fact[m] % MOD * inv_fact[n] % MOD;
}

void prev() {
    fact[0] = 1;
    for (int i = 1; i <= k; ++i) fact[i] = fact[i - 1] * i % MOD;

    inv_fact[k] = inv(fact[k]);
    for (int i = k; i >= 1; --i) inv_fact[i - 1] = inv_fact[i] * i % MOD;
}
```



## Lucas 定理

```cpp
constexpr int MAXN = 100'005;

int fact[MAXN], inv_fact[MAXN];

int t, p, m, n;

int fast_pow(int a, int e) {
    int res = 1;
    a %= p;

    while (e) {
        if (e & 1) res = a * res % p;
        a = a * a % p;
        e >>= 1;
    }

    return res;
}

int inv(int a) {
    return fast_pow(a, p - 2) % p;
}

void prev() {
    fact[0] = 1;
    for (int i = 0; i < p; ++i) fact[i + 1] = fact[i] * (i + 1) % p;

    inv_fact[p - 1] = inv(fact[p - 1]);
    for (int i = p - 1; i > 0; --i) inv_fact[i - 1] = inv_fact[i] * i % p;
}

int comb(int N, int M) {
    if (M < 0 || M > N) return 0;
    return fact[N] * inv_fact[N - M] % p * inv_fact[M] % p;
}

int lucas(int N, int M) {
    if (M == 0) return 1;
    return comb(N % p, M % p) * lucas(N / p, M / p) % p;
}
```



## 帕斯卡递推  /  杨辉三角：

```cpp
constexpr int MAXN = 2'005;

int comb[MAXN][MAXN];
int pre[MAXN + 1][MAXN + 1];

int t, k, m, n;

void prev() {
    comb[0][0] = 1;

    for (int i = 0; i < MAXN - 1; ++i) {
        comb[i][0] = comb[i][i] = 1;

        for (int j = 1; j <= i + 1; ++j) {
            comb[i + 1][j] = (comb[i][j - 1] + comb[i][j]) % k;
        }
    }

    for (int i = 0; i < MAXN; ++i) {
        for (int j = 0; j < MAXN; ++j) {
            pre[i + 1][j + 1] = pre[i + 1][j] + pre[i][j + 1] - pre[i][j] + (comb[i][j] == 0 && j <= i);
        }
    }
}
```



## 分解质因子

```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;

template<typename T>
inline void read(T& x) {
    x = 0; int f = 1; int ch = getchar();
    while (ch != EOF && !isdigit(ch)) { if (ch == '-') f = -1; ch = getchar(); }
    while (ch != EOF && isdigit(ch)) { x = x * 10 + ch - '0'; ch = getchar(); }
    x *= f;
}

constexpr int MAXN = 100'005;
int n, l, r, p, fact[MAXN], cnt[MAXN][20];
vector<int> prime;

int fast_pow(int a, int x, int mod) {
    int res = 1;
    while (x) {
        if (x & 1) res = res * a % mod;
        a = a * a % mod;
        x >>= 1;
    }
    
    return  res;
}

void exgcd(int a, int b, int& x, int& y) {
    if (b) {
        exgcd(b, a % b, y, x);
        y -= a / b * x;
    } else x = 1, y = 0;
}

int inv(int a, int mod) {
    int x, y;
    exgcd(a, mod, x, y);
    return (x % mod + mod) % mod;
}

void factorize(int x) {
    for (int i = 2; i * i <= x; ++i) {
        if (x % i == 0) {
            prime.emplace_back(i);
            while (x % i == 0) x /= i;
        }
    }
    if (x > 1) prime.emplace_back(x);
}

void pre() {
    fact[0] = 1 % p;
    for (int j = 0; j < prime.size(); ++j) cnt[0][j] = 0;

    for (int i = 1; i <= n; ++i) {
        int x = i;
        for (int j = 0; j < prime.size(); ++j) {
            cnt[i][j] = cnt[i - 1][j];
            while (x % prime[j] == 0) {
                x /= prime[j];
                ++cnt[i][j];
            }
        }

        fact[i] = fact[i - 1] * x % p;
    }
}

int comb(int n, int m) {
    if (m > n || m < 0 || n < 0) return 0;

    int res = fact[n] * inv(fact[m], p) % p * inv(fact[n - m], p) % p;
    for (int j = 0; j < prime.size(); ++j) {
        int exp = cnt[n][j] - cnt[m][j] - cnt[n - m][j];
        if (exp == 0) continue;
        res = res * fast_pow(prime[j], exp, p) % p;
    }

    return res;
}

signed main() {
    read(n), read(p), read(l), read(r);
    factorize(p), pre();

    int ans = 0;
    for (int i = 0; i <= n; ++i) {
        ans = (ans + comb(n, i) * (comb(i, (i + l + 1) / 2) - comb(i, (i + r) / 2 + 1)) % p + p) % p;
    }

    cout << ans;
}
```





## 错排数（洛谷4071，CF888D）

```cpp
void pre() {
    d[0] = 1, d[1] = 0, d[2] = 1;
    for (int i = 3; i < MAXN; ++i)
        d[i] = (i - 1) * (d[i - 1] + d[i - 2]);
}
```

