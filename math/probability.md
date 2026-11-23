## 概率正推（CF148D）

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

int b, w;

long double dfs(vector<vector<long double>>& memo, int n, int m) {
    if (n == 0) return 0;
    if (m == 0) return 1;
    if (memo[n][m] != -1) return memo[n][m];

    long double a = (long double)n + m;
    long double res = n / a;
    if (m >= 2) res += m / a * ((m - 1) / (a - 1) * (n / a - 2) * dfs(memo, n - 1, m - 2));
    if (m >= 3) res += m / a * ((m - 1) / (a - 1) * ((m - 2) / (a - 2)) * dfs(memo, n, m - 3));

    return memo[n][m] = res;
}

signed main() {
    read(w), read(b);
    vector<vector<long double>> memo(w + 1, vector<long double>(b + 1, -1));
    cout << fixed << setprecision(9) << dfs(memo, w, b);
}
```



## 马尔科夫链（CF540D）

```cpp
#include <bits/stdc++.h>
#define int long long
#define alpha (i * j + j * k + i * k)
#define alpha1 i * j
#define alpha2 i * k
#define alpha3 j * k
using namespace std;

template<typename T>
inline void read(T& x) {
    x = 0; int f = 1; int ch = getchar();
    while (ch != EOF && !isdigit(ch)) { if (ch == '-') f = -1; ch = getchar(); }
    while (ch != EOF && isdigit(ch)) { x = x * 10 + ch - '0'; ch = getchar(); }
    x *= f;
}

constexpr int MAXN = 105;
int r, s, p;

double f[MAXN][MAXN][MAXN], g[MAXN][MAXN][MAXN];

double dfs_f(int i, int j, int k) {
    if (i <= 0) return 0;
    if (k <= 0) return 1;
    if (j <= 0) return 0;
    if (f[i][j][k] != -1.0) return f[i][j][k];

    double res = 0;
    res += (double)alpha1 / alpha * dfs_f(i, j - 1, k);
    res += (double)alpha2 / alpha * dfs_f(i - 1, j, k);
    res += (double)alpha3 / alpha * dfs_f(i, j, k - 1);

    return f[i][j][k] = res;
}

double dfs_g(int i, int j, int k) {
    if (j <= 0) return 0;
    if (i <= 0) return 1;
    if (k <= 0) return 0;
    if (g[i][j][k] != -1.0) return g[i][j][k];

    double res = 0;
    res += (double)alpha1 / alpha * dfs_g(i, j - 1, k);
    res += (double)alpha2 / alpha * dfs_g(i - 1, j, k);
    res += (double)alpha3 / alpha * dfs_g(i, j, k - 1);

    return g[i][j][k] = res;
}


signed main() {
    read(r), read(s), read(p);
    fill_n(&f[0][0][0], MAXN * MAXN * MAXN, -1.0);
    fill_n(&g[0][0][0], MAXN * MAXN * MAXN, -1.0);

    double ans1 = dfs_f(r, s, p), ans2 = dfs_g(r, s, p), ans3 = 1 - ans1 - ans2;
    cout << fixed << setprecision(10) << ans1 << ' ' << ans2 << ' ' << ans3;
}
```

