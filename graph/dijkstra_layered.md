## 分层图最短路：（洛谷4568，4822，2939）

```cpp
#include <bits/stdc++.h>
using namespace std;

int a = []{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    return 0;
}();

using ll = long long;
const int MAXN = 1e4 + 10;
const ll INF = 4e18;
using tp = tuple<ll, int, int>;
int m, n, k, s, t;

vector<pair<int, int>> e[MAXN];
ll dist[MAXN][12];

void dijkstra(int s) {
    fill(dist[0], dist[0] + MAXN * 12, INF);
    priority_queue<tp, vector<tp>, greater<>> pq;
    dist[s][0] = 0;
    pq.emplace(0, s, 0);

    while (!pq.empty()) {
        auto [d, u, cnt] = pq.top(); pq.pop();
        if (d > dist[u][cnt]) continue;

        for (auto& [v, w] : e[u]) {
            if (d + w < dist[v][cnt]){
                dist[v][cnt] = d + w;
                pq.emplace(dist[v][cnt], v, cnt);
            }

            if (cnt < k && d < dist[v][cnt + 1]) {
                dist[v][cnt + 1] = d;
                pq.emplace(dist[v][cnt + 1], v, cnt + 1);
            }
        }
    }
}

int main() {
    cin >> n >> m >> k >> s >> t;

    for (int i = 0; i < m; ++i) {
        int u, v, w;
        cin >> u >> v >> w;
        e[u].emplace_back(v, w);
        e[v].emplace_back(u, w);
    }

    dijkstra(s);
    cout << ranges::min(dist[t]);
}
```

## 最短路二分：（洛谷1462，LC3620）

```cpp
#include <bits/stdc++.h>
using namespace std;

int a = []{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    return 0;
}();

using ll = long long;
const int MAXN = 1e4 + 10;
const ll INF = 4e18;
using pi = pair<ll, int>;

vector<pair<int, int>> e[MAXN];
ll dist[MAXN];
int cost[MAXN];

int m, n, b;

int main() {
    cin >> n >> m >> b;
    for (int i = 0; i < n; ++i) cin >> cost[i + 1];

    for (int i = 0; i < m; ++i) {
        int u, v, w;
        cin >> u >> v >> w;
        e[u].emplace_back(v, w);
        e[v].emplace_back(u, w);
    }

    int left = -1, right = 1e9 + 1;				//left = -1，一定不可行

    auto check = [&](int limit) -> bool {
        if (cost[1] > limit || cost[n] > limit) return false;	//起点或终点不可达

        priority_queue<pi, vector<pi>, greater<>> pq;
        fill(dist, dist + MAXN, INF);
        dist[1] = 0;
        pq.emplace(0, 1);
    
        while (!pq.empty()) {
            auto [d, u] = pq.top(); pq.pop();
            if (d > dist[u]) continue;
            if (cost[u] > limit) continue;				//limit限制超权的点不进图
            
            for (auto& [v, w] : e[u]) {
                if (w + dist[u] < dist[v]) {
                    dist[v] = w + dist[u];
                    pq.emplace(dist[v], v);
                }
            }
        }
        
        return dist[n] <= b;
    };

    if (!check(1e9)) {
        cout << "AFK\n";
        return 0;
    }

    while (left + 1 < right) {
        int mid = left + (right - left) / 2;
        (check(mid) ? right : left) = mid;
    }

    cout << right;
}
```

