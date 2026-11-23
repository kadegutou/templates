## 判定二分图（LC785）：

```cpp
class Solution {
public:
    bool isBipartite(vector<vector<int>>& graph) {
        vector<int> colors(graph.size(), 0);

        auto dfs = [&](this auto&& dfs, int node, int color) -> bool {
            colors[node] = color;

            for (int& next : graph[node]) {
                if (colors[next] == color) return false;
                if (colors[next] == 0 && !dfs(next, -color)) return false;
            }

            return true;
        };

        for (int i = 0; i < graph.size(); ++i) {
            if (colors[i] == 0 && !dfs(i, 1)) return false;
        }

        return true;
    }
};
```



## 匈牙利算法（洛谷3386，洛谷1129（数学建模难））：

```cpp
#include <bits/stdc++.h>
using namespace std;

int fi = []{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    return 0;
}();

int n, m, k, u, v;
const int MAXN = 1e3 + 5;
vector<int> e[MAXN];
bool vis[MAXN];
int match_r[MAXN];

bool dfs(int node) {
    for (int& next : e[node]) {
        if (vis[next]) continue;
        vis[next] = true;

        if (match_r[next] == 0 || dfs(match_r[next])) {
            match_r[next] = node;
            return true;
        }
    }

    return false;
}

int main() {
    cin >> n >> m >> k;
    for (int i = 0; i < k; ++i) {
        cin >> u >> v;
        e[u].emplace_back(v + n);			//建边是单向的（左→右）
    }

    int ans = 0;
    for (int i = 1; i <= n; ++i) {
        fill(vis, vis + MAXN, false);			//vis[] 标记每轮 DFS 要清空（不清会 WA）
        if (dfs(i)) ++ans;
    }
    cout << ans;
}
```



## 配对输出（洛谷2756）：

```cpp
#include <bits/stdc++.h>
using namespace std;

int fi = []{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    return 0;
}();

int n, m, u, v;
const int MAXN = 1e2 + 5;
vector<int> e[MAXN];
bool vis[MAXN];
int match_r[MAXN];

bool dfs(int node) {
    for (int& next : e[node]) {
        if (vis[next]) continue;
        vis[next] = true;

        if (match_r[next] == 0 || dfs(match_r[next])) {
            match_r[next] = node;
            return true;
        }
    }

    return false;
}

int main() {
    cin >> m >> n >> u >> v;
    while (u != - 1 && v != -1) {
        e[u].emplace_back(v);
        cin >> u >> v;
    }

    int cnt = 0;
    for (int i = 1; i <= m; ++i) {
        fill(vis, vis + MAXN, false);
        if(dfs(i)) ++cnt;
    }
    cout << cnt << '\n';

    for (int i = m + 1; i <= n; ++i)
        if (match_r[i] != 0) cout << match_r[i] << ' ' << i << '\n';
}
```

