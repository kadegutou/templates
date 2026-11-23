## 有负边权（洛谷3385）：

```cpp
#include <bits/stdc++.h>
using namespace std;

int z = []{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    return 0;
}();

const int MAXN = 2e3 + 10;
const int INF = 1e9;
vector<pair<int, int>> e[MAXN];
long long dis[MAXN], cnt[MAXN];
bool inq[MAXN];

int n, t, m, u, v, w;

bool spfa() {
    fill(dis, dis + n + 1, INF);
    fill(cnt, cnt + n + 1, 0);
    fill(inq, inq + n + 1, false);
    queue<int> q;
    dis[1] = 0;
    q.emplace(1);
    inq[1] = true;				//单源起点回答的是"源点可达的负环"，超级源点（全员入队）回答的是"全图有无负环"，改成for (int i = 0; i < n; ++i) q.emplace(i + 1); 同时dis填充成fill(dis, dis + n + 1, 0); inq填充成fill(inq, inq + n + 1, true);
    
    while (!q.empty()) {
        int u = q.front(); q.pop();
        inq[u] = false;

        for (auto& [v, w] : e[u]) {
            if (dis[u] + w < dis[v]) {
                dis[v] = dis[u] + w;
                cnt[v] = cnt[u] + 1;
                if (cnt[v] >= n) return true;
                if (!inq[v]) {
                    q.emplace(v);
                    inq[v] = true;
                }
            }
        }
    }

    return false;
}

int main() {
    cin >> t;
    while (t--) {
        cin >> n >> m;
        for (int i = 0; i < n; ++i) e[i + 1].clear();
        
        while (m--) {
            cin >> u >> v >> w;
            e[u].emplace_back(v, w);
            if (w >= 0) e[v].emplace_back(u, w);
        }

        cout << (spfa() ? "YES\n" : "NO\n");
    }
}
```



## 全源最短路（Johnson算法，洛谷5905）：

```cpp
#include <bits/stdc++.h>
using namespace std;

int z = []{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    return 0;
}();

using ll = long long;
const int MAXN = 3e3 + 10;
const ll INF = 4e18;
using pi = pair<int, int>;

vector<pi> e[MAXN];
vector<pi> je[MAXN];
ll dis[MAXN];
int cnt[MAXN], h[MAXN];
bool inq[MAXN];
deque<int> dq;

int n, m, u, v, w;
long long total = 0;

bool lsf_spfa() {
    fill(h, h + MAXN, INF);
    fill(cnt, cnt + MAXN, 0);
    fill(inq, inq + MAXN, true);
    h[0] = 0;
    for (int i = 0; i <= n; ++i) dq.emplace_back(i);

    while (!dq.empty()) {
        auto u = dq.front(); dq.pop_front();
        inq[u] = false;

        for (auto& [v, w] : e[u]) {
            if (h[u] + w < h[v]) {
                h[v] = h[u] + w;
                cnt[v] = cnt[u] + 1;
                if (cnt[v] >= n) return true;
                
                if (!inq[v]) {
                    inq[v] = true;
                    if(dq.empty() || h[v] < h[dq.front()]) dq.emplace_front(v);
                    else dq.emplace_back(v);
                }
            }
        }
    }

    return false;
}

void dijkstra(int s) {
    priority_queue<pi, vector<pi>, greater<>> pq;
    fill(dis, dis + MAXN, INF);
    dis[s] = 0;
    pq.emplace(0, s);

    while (!pq.empty()) {
        auto [d, u] = pq.top(); pq.pop();
        if (d > dis[u]) continue;

        for (auto& [v, w] : je[u]) {
            if (d + w < dis[v]) {
                dis[v] = d + w;
                pq.emplace(d + w, v);
            }
        }
    }
}

int main() {
    cin >> n >> m;
        
    while (m--) {
        cin >> u >> v >> w;
        e[u].emplace_back(v, w);
    }
    for (int i = 1; i <= n; ++i) e[0].emplace_back(i, 0);
    
    if (lsf_spfa()) cout << -1;
    else {
        for (int u = 1; u <= n; ++u)
            for (auto& [v, w] : e[u])
                je[u].emplace_back(v, w + h[u] - h[v]);
        
        for (int i = 1; i <= n; ++i) {
            dijkstra(i);
            for (int j = 1; j <= n; ++j)
                total += j * (dis[j] >= INF / 2 ? 1e9 : dis[j] + h[j] - h[i]);
            cout << total << '\n';
            total = 0;
        }
    }
}
```

