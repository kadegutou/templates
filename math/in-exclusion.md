## 容斥原理

说明：`容斥原理没有什么公式，大都是应用，所以这些都是各种栗子的做法参考`

### 洛谷1450

```cpp
constexpr int MAXS = 100'005;
int c[5], d[5], n, s, f[MAXS];

void pre() {
    f[0] = 1;
    for (int i = 1; i <= 4; ++i) {
        for (int j = c[i]; j < MAXS; ++j) {
            f[j] += f[j - c[i]];
        }
    }
}


signed main() {
    for (int i = 1; i <= 4; ++i) read(c[i]);
    read(n);
    pre();
    
    while (n--) {
        for (int i = 1; i <= 4; ++i) read(d[i]);
        read(s);

        int ans = 0;
        for (int mask = 0; mask < 16; ++mask) {
            int sum = 0, bits = 0;
            for (int i = 1; i <= 4; ++i) {
                if (mask >> (i - 1) & 1) {
                    ++bits;
                    sum += (d[i] + 1) * c[i];
                }
            }

            if (s - sum >= 0) {
                if (bits & 1) ans -= f[s - sum];
                else ans += f[s - sum];
            }
        }

        cout << ans << '\n';
    }
}

```



### CF839D

#### 💡 提示一：转化问题，枚举 `gcd`

不要直接去枚举数组的子序列。注意到数组元素的值域是 ai≤10^6，这提示我们可以**枚举 gcd 的候选值 d（从 2 到 max(ai)）**。

对于每个 d*d*，我们想要求出“**所有 gcd 恰好等于 dd 的子序列**”对答案的贡献。这个贡献就是 `(gcd 的值 d) × (这些子序列的长度之和)`。

#### 💡 提示二：先“放宽条件”，再“容斥”

直接求“`gcd` 恰好为 d”很难，但求“`gcd` 是 d 的倍数”很容易。

1. **统计倍数数量**：对于每个 d，遍历它的所有倍数（d,2d,3d,…*d*,2*d*,3*d*,…），统计数组中这些倍数一共出现了多少次，记为 `cnt[d]`。
2. **计算“放宽后”的总长度**：如果只考虑这些 `cnt[d]` 个数，它们形成的所有非空子序列的 `gcd` **至少**是 d（即 `gcd` 是 d 的倍数）。这些子序列的总长度有一个简洁的公式：**cnt[d] × 2^(cnt[d] - 1)**。

#### 💡 提示三：用容斥“筛出”精确的 `gcd`

`cnt[d] × 2^(cnt[d] - 1)` 计算出的总长度，包含了 `gcd` 为 d,2d,3d,…*d*,2*d*,3*d*,… 的所有子序列。要得到 `gcd` **恰好**为 d 的长度和 `f[d]`，我们需要**减去**那些 `gcd` 为 d的更大倍数的子序列长度。

因此，我们可以**从大到小**枚举 d，递推公式为：

> **f[d] = cnt[d] × 2^(cnt[d] - 1) - f[2d] - f[3d] - ...**

其中 `f[2d]`, `f[3d]` 等在枚举到 d 时已经计算好了。

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

constexpr int MOD = 1'000'000'007, MAXM = 1'000'005;
int n, len[MAXM], cnt[MAXM], freq[MAXM], mx = 0, x;

int fast_pow(int a, int x) {
    int res = 1;
    while (x) {
        if (x & 1) res = res * a % MOD;
        a = a * a % MOD;
        x >>= 1;
    }

    return res;
}

signed main() {
    read(n);
    for (int i = 1; i <= n; ++i) {
        read(x);
        ++freq[x];
        mx = max(mx, x);
    }

    for (int i = 1; i <= mx; ++i) {
        for (int multiple = i; multiple <= mx; multiple += i) {
            cnt[i] += freq[multiple];
        }
    }

    for (int i = mx; i >= 1; --i) {
        // 注意当 cnt[i] == 0 时，cnt[i] - 1 = - 1，-1 >> 1 = -1，会一直死循环
        if (cnt[i] == 0) {
            len[i] = 0;
            continue;
        }
        
        len[i] = cnt[i] * fast_pow(2, cnt[i] - 1) % MOD;
        for (int j = 2 * i; j <= mx; j += i) {
            len[i] = (len[i] - len[j] + MOD) % MOD;
        }
    }

    int ans = 0;
    for (int i = 2; i <= mx; ++i) ans = (ans + i * len[i] % MOD) % MOD;

    cout << ans;
}
```



### CF451E

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

constexpr int MAXN = 25, MOD = 1'000'000'007;
int n, s, f[MAXN], fact[MAXN], inv_fact[MAXN];

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
    if (n < 0 || m > n) return 0;

    int res = 1;
    for (int i = 0; i < m; ++i) res = res * ((n - i) % MOD) % MOD;

    return res * inv_fact[m] % MOD;
}

signed main() {
    read(n), read(s);
    pre();
    for (int i = 1; i <= n; ++i) read(f[i]);

    int ans = 0;
    for (int mask = 0; mask < 1LL << n; ++mask) {
        int sum = 0, bits = 0;
        
        for (int j = 1; j <= n; ++j) {
            if (mask >> (j - 1) & 1) {
                ++bits;
                sum += f[j] + 1;
            }
        }

        if (bits & 1) ans = (ans - comb(s - sum + n - 1, n - 1) + MOD) % MOD;
        else ans = (ans + comb(s - sum + n - 1, n - 1)) % MOD;
    }

    cout << ans;
}
```

