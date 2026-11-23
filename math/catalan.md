## Catalan 通项公式版

```cpp
int catalan(int n) {
    return comb(2 * n, n) * inv(n + 1) % MOD;
}
```



## Catalan 递推版

```cpp
int catalan(int n) {
    cat[0] = 1;
    for (int i = 1; i < MAXN; ++i) {
        cat[i] = 0;
        for (int j = 0; j < i; ++j)
            cat[i] = (cat[i] + cat[j] * cat[i - 1 - j] % MOD + MOD) % MOD;
    }
    
    return cat[n];
}
```



## 实例分析CF1924D

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

constexpr int MOD = 1'000'000'007, MAXN = 4'005;
int t, n, m, k, fact[MAXN], inv_fact[MAXN];

int fast_pow(int a, int x) {
    int res = 1;
    while (x) {
        if (x & 1) res = res * a % MOD;
        a = a * a % MOD;
        x >>= 1;
    }

    return res;
}

void pre() {
    fact[0] = 1;
    for (int i = 1; i < MAXN; ++i) fact[i] = fact[i - 1] * i % MOD;
    for (int i = 0; i < MAXN; ++i) inv_fact[i] = fast_pow(fact[i], MOD - 2);
}

int comb(int n, int m) {
    if (n < 0 || m > n || m < 0) return 0;
    return fact[n] * inv_fact[m] % MOD * inv_fact[n - m] % MOD;
}

signed main() {
    pre();
    read(t);
    while (t--) {
        read(n), read(m), read(k);
        if (k > min(n, m)) {
            cout << 0 << '\n';
            continue;
        }

        cout << (comb(n + m, k) - comb(n + m, k - 1) + MOD ) % MOD<< '\n';
    }
}
```

