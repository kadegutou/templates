## 最短路：（洛谷4779，3371）

```cpp
const int MAXN = 1e5 + 10;
const long long INF = 4e18;

vector<pair<int, int>> e[MAXN];  // e[u] = {(v, w)}
long long dis[MAXN];

void dijkstra(int s) {
    priority_queue<pair<long long, int>,
                   vector<pair<long long, int>>,
                   greater<>> q;
    fill(dis, dis + MAXN, INF);
    dis[s] = 0;
    q.emplace(0, s);
    while (!q.empty()) {
        auto [d, u] = q.top();
        q.pop();
        if (d > dis[u]) continue;        // 懒删除：过期副本
        for (auto [v, w] : e[u]) {
            if (dis[u] + w < dis[v]) {
                dis[v] = dis[u] + w;
                q.emplace(dis[v], v);
            }
        }
    }
}
```



## 次短路：（洛谷2865）

```cpp
#include <bits/stdc++.h>
using namespace std;

const int MAXN = 5010;
const long long INF = 4e18;

vector<pair<int, int>> e[MAXN];
long long d1[MAXN], d2[MAXN];   // 最短 / 次短

int main() {
    int n, m;
    scanf("%d %d", &n, &m);
    while (m--) {
        int u, v, w;
        scanf("%d %d %d", &u, &v, &w);
        e[u].emplace_back(v, w);
        e[v].emplace_back(u, w);   // P2865 是无向图，别漏反向边
    }

    fill(d1, d1 + n + 1, INF);
    fill(d2, d2 + n + 1, INF);
    d1[1] = 0;

    priority_queue<pair<long long, int>,
                   vector<pair<long long, int>>, greater<>> q;
    q.emplace(0, 1);
    while (!q.empty()) {
        auto [d, u] = q.top();
        q.pop();
        if (d > d2[u]) continue;            // 关键：用 d2 剪枝，不是 d1！
        for (auto [v, w] : e[u]) {
            long long nd = d + w;
            if (nd < d1[v]) {               // 新最短登基，老最短降级
                d2[v] = d1[v];
                d1[v] = nd;
                q.emplace(d1[v], v);
                q.emplace(d2[v], v);
            } else if (d1[v] < nd && nd < d2[v]) {        // 争严格次短，非严格次段去掉d1[v] < nd
                d2[v] = nd;
                q.emplace(d2[v], v);
            }
        }
    }
    printf("%lld\n", d2[n]);
    return 0;
}
```

## 求路径：（CF 20C）

```cpp
#include <bits/stdc++.h>
using namespace std;

int a = []{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    return 0;
}();

using ll = long long;
const int MAXN = 1e5 + 10;
const ll INF = 4e18;
using pi = pair<ll, int>;

int pre[MAXN];
vector<pair<int, int>> e[MAXN];
ll dist[MAXN];

void dijkstra() {
    priority_queue<pi, vector<pi>, greater<>> pq;
    fill(dist, dist + MAXN, INF);
    dist[1] = 0;
    pq.emplace(0, 1);

    while (!pq.empty()) {
        auto [d, u] = pq.top(); pq.pop();
        if (d > dist[u]) continue;

        for (auto& [v, w] : e[u]) {
            if (w + dist[u] < dist[v]) {
                dist[v] = w + dist[u];
                pre[v] = u;
                pq.emplace(dist[v], v);
            }
        }
    }
}

int main() {
    int m, n;
    cin >> n >> m;

    for (int i = 0; i < m; ++i) {
        int u, v, w;
        cin >> u >> v >> w;
        e[u].emplace_back(v, w);
        e[v].emplace_back(u, w);
    }

    dijkstra();
    if (dist[n] == INF) cout << -1 << '\n';
    else {
        vector<int> path;
        for (int cur = n; cur != 0; cur = pre[cur])
            path.emplace_back(cur);
        ranges::reverse(path);
        for (int& p : path)
            cout << p << '';
    }
}
```



