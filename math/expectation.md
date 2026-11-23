## 期望（洛谷1365，1654，2221）

```cpp
#include <bits/stdc++.h>
using namespace std;

template<typename T>
inline void read(T& x) {
    x = 0; int f = 1; int ch = getchar();
    while (ch != EOF && !isdigit(ch)) { if (ch == '-') f = -1; ch = getchar(); }
    while (ch != EOF && isdigit(ch)) { x = x * 10 + ch - '0'; ch = getchar(); }
    x *= f;
}

string s;
int n;
long double e1, e2;

signed main() {
    read(n);
    cin >> s;

    // 首位特判是坑，统一循环更稳
    if (s[0] == 'o') e1 = 1, e2 = 1;
    else if (s[0] == '?') e1 = 0.5, e2 = 0.5;
    long double ans = e2;

    for (int i = 1; i < n; ++i) {
        if (s[i] == 'o') {
            e2 += 2 * e1 + 1;
            ans += 2 * e1++ + 1;
        }
        else if (s[i] == '?') {
            ans += e1 + 0.5;
            e2 = 0.5 * (e2 + 2 * e1 + 1);
            e1 = 0.5 * (e1 + 1);
        } else e1 = 0, e2 = 0;
    }

    cout << fixed << setprecision(4) << ans;
}
```



## 极值期望恒等式（CF453A）

```cpp
#include <bits/stdc++.h>
using namespace std;

template<typename T>
inline void read(T& x) {
    x = 0; int f = 1; int ch = getchar();
    while (ch != EOF && !isdigit(ch)) { if (ch == '-') f = -1; ch = getchar(); }
    while (ch != EOF && isdigit(ch)) { x = x * 10 + ch - '0'; ch = getchar(); }
    x *= f;
}

long double fast_pow(long double a, int x) {
    long double res = 1;
    while (x) {
        if (x & 1) res = res * a;
        a = a * a;
        x >>= 1;
    }

    return res;
}

int n, m;

signed main() {
    read(m), read(n);
    long double ans = 0;
    for (int i = 1; i <= m; ++i) {
        ans += (long double)i * (fast_pow((long double)i / m, n) - fast_pow(((long double)i - 1) / m, n));
    }

    cout << fixed << setprecision(10) << ans;
}
```



## DAG 期望逆推（洛谷4316）

```cpp
// 记忆化搜索
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
vector<pair<int, int>> edge[MAXN];
int n, m, u, v, w;
vector<long double> memo(MAXN, -1);

long double dfs(int u) {
    if (u == n) return 0;
    if (memo[u] != -1) return memo[u];

    long double res = 0;
    int sz = edge[u].size();
    for (auto& [v, w] : edge[u]) {
        res += (dfs(v) + w) / sz;
    }

    return memo[u] = res;
}

signed main() {
    read(n), read(m);
    for (int i = 1; i <= m; ++i) {
        read(u), read(v), read(w);
        edge[u].emplace_back(v, w);
    }
    
    cout << fixed << setprecision(2) << dfs(1);
}
```

```cpp
// 反向图 + 拓扑序倒推
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
vector<pair<int, int>> edge[MAXN];
int n, m, u, v, w;
queue<int> q;

signed main() {
    read(n), read(m);
    vector<long double> f(n + 1, 0);
    vector<int> indeg(n + 1, 0), deg(n + 1, 0);
    f[n] = 0;
    for (int i = 1; i <= m; ++i) {
        read(u), read(v), read(w);
        edge[v].emplace_back(u, w);
        ++indeg[u];
        ++deg[u];
    }

    for (int i = 1; i <= n; ++i)
        if (indeg[i] == 0)
            q.emplace(i);

    while (!q.empty()) {
        u = q.front(); q.pop();

        for (auto& [v, w] : edge[u]) {
            if (--indeg[v] == 0) q.emplace(v);
            f[v] += (f[u] + w) / deg[v];
        }
    }

    cout << fixed << setprecision(2) << f[1];
}
```



## 期望正推（洛谷1850）

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

constexpr int MAXN = 2'005, MAXV = 305;

int n, m, c_, e, c[MAXN], d[MAXN], x, y, w;
double k[MAXN], memo[MAXN][MAXN][2];
vector<pair<int, int>> edge[MAXV];

void floyd(vector<vector<int>>& dis, int class_cnt) {
    for (int u = 1; u <= class_cnt; ++u) {
        dis[u][u] = 0;
        for (auto& [v, w] : edge[u]) {
            dis[u][v] = min(dis[u][v], w);
            dis[v][u] = min(dis[v][u], w);
        }
    }

    for (int l = 1; l <= class_cnt; ++l) {
        for (int i = 1; i <= class_cnt; ++i) {
            for (int j = 1; j <= class_cnt; ++j) {
                dis[i][j] = min(dis[i][j], dis[l][j] + dis[i][l]);           
            }
        }
    }
}

double dfs(vector<vector<int>>& dis, int a, int b, int ok) {
    if (b == n) return 0;
    if (memo[a][b][ok] != -1) return memo[a][b][ok];

    double res;
    double k1 = k[b], k2 = k[b + 1];
    int c1 = c[b], c2 = c[b + 1], d1 = d[b], d2 = d[b + 1];

    if (ok) {
        res = dfs(dis, a, b + 1, 0) + dis[d1][c2] * k1 + dis[c1][c2] * (1 - k1);
        if (a >= 1) {
            double tmp = dfs(dis, a - 1, b + 1, 1);
            res = min(res, tmp + dis[d1][d2] * k1 * k2 + dis[d1][c2] * k1 * (1 - k2) + dis[c1][c2] * (1 - k1) * (1- k2) + dis[c1][d2] * (1 - k1) * k2);
        }
    } else {
        res = dfs(dis, a, b + 1, 0) + dis[c1][c2];
        if (a >= 1) {
            double tmp = dfs(dis, a - 1, b + 1, 1);
            res = min(res, tmp + dis[c1][d2] * k2 + dis[c1][c2] * (1 - k2));
        }
    }

    return memo[a][b][ok] = res;
}

signed main() {
    read(n), read(m), read(c_), read(e);
    for (int i = 1; i <= n; ++i) read(c[i]);
    for (int i = 1; i <= n; ++i) read(d[i]);
    for (int i = 1; i <= n; ++i) cin >> (k[i]);
    for (int i = 1; i <= e; ++i) {
        read(x), read(y); read(w);
        edge[x].emplace_back(y, w);
        edge[y].emplace_back(x, w);
    }

    vector<vector<int>> dis(c_ + 1, vector<int>(c_ + 1, 0x3f3f3f3f));
    floyd(dis, c_);
    fill(&memo[0][0][0], &memo[0][0][0] + MAXN * MAXN * 2, -1.0);

    double ans0 = dfs(dis, m, 1, 0), ans1 = m >= 1 ? dfs(dis, m - 1, 1, 1) : ans0;
    cout << fixed << setprecision(2) << min(ans0, ans1);
}
```



## 期望逆推（洛谷4550，2473，CF908D）

```cpp
/*
请思考：如果定义 f[i][j] 表示已经买了 i 次且手上有 j 张不同的邮票，那转移方程应该是怎么样的，这样的转移方程有什么问题。以及如何从这样定义的 f[i][j] 推导到正确做法的 f[j] 和 g[j]
*/
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

constexpr int MAXN = 10'005;
int n;
double f[MAXN], g[MAXN];

signed main() {
    read(n);
    for (int i = n; i > 0; --i) {
        double alpha = (double)n / (n - i + 1);
        f[i - 1] = f[i] + alpha;
        g[i - 1] = g[i] + alpha * f[i - 1];
    }

    cout << fixed << setprecision(2) << g[0];
}
```



## 期望线性性（洛谷1297，3802）

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

constexpr int MAXN = 10'000'005;
int n, A, B, C, a[MAXN];


signed main() {
    scanf("%d%d%d%d%d", &n, &A, &B, &C, a + 1);
    for (int i = 2; i <= n; i++)
	    a[i] = ((long long)a[i - 1] * A + B) % 100000001;
    for (int i = 1; i <= n; i++)
        a[i] = a[i] % C + 1;

    long double ans = 0;
    for (int i = 1; i < n; ++i) {
        ans += (long double)min(a[i], a[i + 1]) / (a[i] * a[i + 1]);
    }
    ans += (long double)min(a[n], a[1]) / (a[n] * a[1]);
    cout << fixed << setprecision(3) << ans;
}
```



## 涉及无穷这个过程的期望递推（CF908D）

```cpp
// 一般出现无穷这个过程都会用到数学的结论（级数求和），典型地，几何分布求第一次伯努利实验成功次数的期望：1 / p。
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

constexpr int MOD = 1'000'000'007;
int k, pa, pb;

int fast_pow(int a, int x) {
    int res = 1;
    while (x) {
        if (x & 1) res = res * a % MOD;
        a = a * a % MOD;
        x >>= 1;
    }

    return res;
}

int inv(int x) {
    return fast_pow(x, MOD - 2);
}

// 返回 (分子, 分母)，值 = 分子 * inv(分母) mod MOD
pair<int, int> dfs(vector<vector<pair<int, int>>>& memo, int i, int j) {
    if (i + j >= k) return pair((pa + (i + j) % MOD * pb) % MOD, pb);
    if (memo[i][j].first != -1) return memo[i][j];

    pair<int, int> res, s1 = dfs(memo, i + 1, j), s2 = dfs(memo, i, i + j);
    res.first = (pa * s1.first % MOD * s2.second % MOD + pb * s2.first % MOD * s1.second % MOD) % MOD;
    res.second = s1.second * s2.second % MOD * (pa + pb) % MOD;

    return memo[i][j] = res;
}

signed main() {
    read(k), read(pa), read(pb);
    vector<vector<pair<int, int>>> memo(k + 1, vector<pair<int, int>>(k + 1, pair(-1, -1)));

    pair<int, int> ans = dfs(memo, 1, 0);
    cout << ans.first * inv(ans.second) % MOD;
}
```
