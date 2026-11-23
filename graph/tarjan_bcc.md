## e - bcc 模板（LC1192，洛谷2860，洛谷8436）

```cpp
#include <bits/stdc++.h>
using namespace std;

constexpr int MAXN = 5005;   // 根据题目调整

vector<pair<int,int>> edge[MAXN]; // 邻接表，pair(邻居, 边编号)
int dfn[MAXN], low[MAXN], bcc_id[MAXN];
bool is_bridge[MAXM];
int timestamp = 0, bcc_cnt = 0;
int n, m;

// Tarjan 找桥
void tarjan(int u, int pre_edge_id) {
    dfn[u] = low[u] = ++timestamp;
    for (auto& [v, id] : edge[u]) {
        if (id == pre_edge_id) continue;  // 跳过刚走过来的边
        if (!dfn[v]) {
            tarjan(v, id);
            low[u] = min(low[u], low[v]);
            if (low[v] > dfn[u]) {
                is_bridge[id] = true;
            }
        } else {
            low[u] = min(low[u], dfn[v]);
        }
    }
}

// 染色，把非桥边连通的点染成同一个颜色
void dfs_color(int u, int color) {
    bcc_id[u] = color;
    for (auto& [v, id] : edge[u]) {
        if (is_bridge[id]) continue;  // 桥不能走
        if (bcc_id[v] == 0) {
            dfs_color(v, color);
        }
    }
}

// 主函数中：
int main() {
    // ... 读入 n, m 并建图（边编号从0开始）...
    for (int i = 0; i < m; ++i) {
        int u, v;
        cin >> u >> v;
        edge[u].emplace_back(v, i);
        edge[v].emplace_back(u, i);
    }

    // 求桥（图可能不连通）
    for (int i = 1; i <= n; ++i)
        if (!dfn[i]) tarjan(i, -1);

    // 染色求边双分量
    for (int i = 1; i <= n; ++i)
        if (bcc_id[i] == 0) {
            ++bcc_cnt;
            dfs_color(i, bcc_cnt);
        }

    // 此时 bcc_cnt 就是边双分量个数，bcc_id[u] 表示节点 u 所属的分量
    // 可以缩点建新图等...
    return 0;
}
```

## 另一种写法，直接在 Tarjan 中弹双连通分量：

```cpp
int dfn[MAXN], low[MAXN], bcc_id[MAXN], bcc_cnt = 0;
bool is_bridge[MAXM];
stack<int> st;

void tarjan(int u, int pre_edge_id) {
    dfn[u] = low[u] = ++timestamp;
    st.push(u);
    for (auto& [v, id] : edge[u]) {
        if (id == pre_edge_id) continue;
        if (!dfn[v]) {
            tarjan(v, id);
            low[u] = min(low[u], low[v]);
            if (low[v] > dfn[u]) {          // 桥
                is_bridge[id] = true;
                // v 的子树是一个独立的边双分量，弹出栈中节点直到 v
                ++bcc_cnt;
                while (true) {
                    int x = st.top(); st.pop();
                    bcc_id[x] = bcc_cnt;
                    if (x == v) break;
                }
            }
        } else {
            low[u] = min(low[u], dfn[v]);
        }
    }
    // 注意：不能在这里用 if (dfn[u] == low[u]) 弹栈！
}

// 在每个连通块的 DFS 结束后，栈中剩余的点属于同一个边双分量
// 主函数里，对每个未访问的节点调用 tarjan 后，如果栈非空，需要清空并分配编号
for (int i = 1; i <= n; ++i) {
    if (!dfn[i]) {
        tarjan(i, -1);
        if (!st.empty()) {
            ++bcc_cnt;
            while (!st.empty()) {
                int x = st.top(); st.pop();
                bcc_id[x] = bcc_cnt;
            }
        }
    }
}
```





## v - bcc 模板（洛谷3388，3225，5058，8435）：

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

constexpr int MAXN = 500'005;
constexpr int MAXM = 3'000'005;

int dfn[MAXN], low[MAXN];
vector<int> bcc[MAXM];
stack<pair<int, int>> st;
bool vis[MAXN];

int timestamp = 0, bcc_cnt = 0;

vector<pair<int, int>> edge[MAXN];

void tarjan(int u, int pre_id) {
    dfn[u] = low[u] = ++timestamp;

    for (auto& [v, id] : edge[u]) {
        if (id == pre_id) continue;

        if (!dfn[v]) {
            st.emplace(u, v);
            tarjan(v, id);
            low[u] = min(low[u], low[v]);

            if (low[v] >= dfn[u]) {
                ++bcc_cnt;

                while (1) {
                    auto [a, b] = st.top(); st.pop();
                    if (!vis[a]) { bcc[bcc_cnt].emplace_back(a); vis[a] = true; }
                    if (!vis[b]) { bcc[bcc_cnt].emplace_back(b); vis[b] = true; }
                    if (a == u && b == v) break;
                }

                for (int& node : bcc[bcc_cnt]) vis[node] = false;
            }
        } else if (dfn[u] > dfn[v]) {
            st.emplace(u, v);
            low[u] = min(low[u], dfn[v]);
        }
    }
}

int n, m, u, v;

int main() {
    read(n); read(m);

    for (int i = 0; i < m; ++i) {
        read(u); read(v);
        if (u == v) continue;
        edge[u].emplace_back(v, i);
        edge[v].emplace_back(u, i);
    }

    for (int i = 1; i <= n; ++i) {
        if (!dfn[i]) {
            if (edge[i].empty()) {
                ++bcc_cnt;
                bcc[bcc_cnt].emplace_back(i);
            } else {
                tarjan(i, -1);
                if (!st.empty()) {
                    ++bcc_cnt;
                    while (!st.empty()) {
                        auto [a, b] = st.top(); st.pop();
                        if (!vis[a]) { bcc[bcc_cnt].emplace_back(a); vis[a] = true; }
                        if (!vis[b]) { bcc[bcc_cnt].emplace_back(b); vis[b] = true; }
                    }

                    for (int& node : bcc[bcc_cnt]) vis[node] = false;
                }
            }
        }
    }

    cout << bcc_cnt << '\n';
    for (int i = 1; i <= bcc_cnt; ++i) {
        cout << bcc[i].size() << ' ';
        for (int& node : bcc[i]) cout << node << ' ';
        cout << '\n';
    }
}
```

