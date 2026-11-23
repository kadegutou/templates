## SCC 模板（洛谷2863，3387）：

```cpp
#include<bits/stdc++.h>
using namespace std;

int z = []{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    return 0;
}();

constexpr int MAXN = 10005;

vector<int> e[MAXN];
int dfn[MAXN], low[MAXN];
bitset<MAXN> in_stack;
stack<int> st;
vector<int> scc[MAXN];

int timestamp = 0, scc_cnt = 0, n, m, u, v;

void tarjan(int u) {
    dfn[u] = low[u] = ++timestamp;
    st.emplace(u);
    in_stack.set(u);

    for (int& v : e[u]) {
        if (!dfn[v]) {
            tarjan(v);
            low[u] = min(low[u], low[v]);
        } else if (in_stack[v]) {
            low[u] = min(low[u], dfn[v]);
        }
    }

    if (dfn[u] == low[u]) {
        ++scc_cnt;
        int x = -1;

        while (x != u) {
            x = st.top(); st.pop();
            in_stack.reset(x);
            scc[scc_cnt].emplace_back(x);
        }
    }
}

int main() {
    cin >> n >> m;
    for (int i = 0; i < m; ++i) {
        cin >> u >> v;
        e[u].emplace_back(v);
    }

    for (int i = 1; i <= n; ++i)
        if (!dfn[i])
            tarjan(i);

    cout << scc_cnt;
}
```



## 缩点技巧（洛谷3387，2002，2746，2341，1262，2272）：

```cpp
#include<bits/stdc++.h>
using namespace std;

int z = []{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    return 0;
}();

constexpr int MAXN = 10005;

vector<int> e[MAXN];
int low[MAXN], dfn[MAXN];
stack<int> st;
bool in_st[MAXN];

int node[MAXN], scc[MAXN], id[MAXN];
vector<int> new_e[MAXN], indegree;
set<pair<int, int>> edges;
queue<int> q;

int n, m, x, y, timestamp = 0, scc_cnt = 0;


void tarjan(int u) {
    dfn[u] = low[u] = ++timestamp;
    st.emplace(u);
    in_st[u] = true;

    for (int& v : e[u]) {
        if (dfn[v] == 0) {
            tarjan(v);
            low[u] = min(low[u], low[v]);
        } else if (in_st[v]) {
            low[u] = min(low[u], dfn[v]);
        }
    }

    if (low[u] == dfn[u]) {
        ++scc_cnt;
        int x = -1;

        while (x != u) {
            x = st.top(); st.pop();
            scc[scc_cnt] += node[x];
            id[x] = scc_cnt;
            in_st[x] = false;
        }
    }
}

int main() {
    cin >> n >> m;
    for (int i = 1; i <= n; ++i) cin >> node[i];
    for (int i = 0; i < m; ++i) {
        cin >> x >> y;
        e[x].emplace_back(y);
    }

    for (int i = 1; i <= n; ++i)
        if (dfn[i] == 0)
            tarjan(i);

    indegree.resize(scc_cnt + 1);
    for (int u = 1; u <= n; ++u) {
        for (int& v : e[u]) {
            int uid = id[u], vid = id[v];
            
            if (uid != vid && edges.insert({uid, vid}).second) {
                new_e[uid].emplace_back(vid);
                ++indegree[vid];
            }
        }
    }
    
    vector<int> f(scc_cnt + 1);

    for (int i = 1; i <= scc_cnt; ++i) {
        if (indegree[i] == 0) {
            q.emplace(i);
            f[i] = scc[i];
        }
    }

    while (!q.empty()) {
        int u = q.front(); q.pop();

        for (int& v : new_e[u]) {
            f[v] = max(f[v], f[u] + scc[v]);
            if (--indegree[v] == 0) q.emplace(v);
        }
    }

    int ans = 0;
    for (int i = 1; i <= scc_cnt; ++i) ans = max(ans, f[i]);
    cout << ans;
}
```
