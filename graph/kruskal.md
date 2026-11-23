## MST（Kruskal算法，洛谷3366，洛谷1195）：

```cpp
#include <bits/stdc++.h>
using namespace std;

int fi = []{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    return 0;
}();

class UnionFind{
public:
    int n;
    vector<int> parent;

    explicit UnionFind(int _n): n(_n), parent(n) {
        ranges::iota(parent, 0);			//C++23才开始支持，可换成iota(a.begin, a.end(), 0)
    }

    int find(int x) {
        if (x != parent[x]) parent[x] = find(parent[x]);
        return parent[x];
    }

    bool unite(int x, int y) {
        int px = find(x), py = find(y);
        if (px == py) return false;
        parent[px] = py;			//不要错写成类似px = parent[py]这样的无效合并
        return true;
    }
};

using tp = tuple<int, int, int>;
const int MAXN = 5e3 + 5;
int n, m, x, y, z;
vector<tp> e;

int main() {
    cin >> n >> m;
    e.reserve(m);
    for (int i = 0; i < m; ++i) {
        cin >> x >> y >> z;
        e.emplace_back(z, x, y);
    }

    ranges::sort(e);

    UnionFind uf(n + 1);			//1-based要建n + 1大小的并查集, 0-based可用n
    int cost = 0, e_used = 0;

    for (auto& [w, u, v] : e) {
        if (uf.unite(u, v)) {
            cost += w;
            if (++e_used == n - 1) {
                cout << cost;
                return 0;
            }
        }
    }

    if (e_used != n - 1) cout << "orz";			//注意不要写成''
    else cout << cost;			//kruskal算法只要没找到到MST，就一定说明全图不连通（因为初始是n个连通块，每增加一条边，减少一个连通块，而最小生成树一定是一个连通块，意味着最后一定有n - 1条边，否则初始的图不连通）
}
```



## MST（Prim算法，洛谷1546，LC1584）

```cpp
#include <bits/stdc++.h>
using namespace std;

int fi = []{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    return 0;
}();

int n, u, v, w;
const int MAXN = 100;
const int INF = 1e9;
int e[MAXN][MAXN];
int min_dis[MAXN];
bool in_mst[MAXN];

int prim(){
    fill(min_dis, min_dis + MAXN, INF);
    fill(in_mst, in_mst + MAXN, false);
    min_dis[0] = 0;

    int cost = 0;
    
    for (int i = 0; i < n; ++i) {
        int u = -1;
        
        for (int j = 0; j < n; ++j)
            if (!in_mst[j] && (u == -1 || min_dis[u] > min_dis[j]))
                u = j;

        if (u == -1) break;			//选点结束之后若u还是-1，说明图不连通（剩下的点都不可达）
        in_mst[u] = true;
        cost += min_dis[u];

        for (int v = 0; v < n; ++v) {
            if (!in_mst[v])
                if (e[v][u] < min_dis[v])
                    min_dis[v] = e[v][u];		//每次选点是把u加入MST，所以是更新v到新成员u的距离
        }
    }

    return cost;
}

int main() {
    cin >> n;
    for (int i = 0; i < n; ++i) {
        for (int j = 0; j < n; ++j) {
            cin >> e[i][j];
        }
    }

    cout << prim();
}
```



## 堆优化的Prim算法（注意在稠密图中，堆优化反而是负优化，一般用于稀疏图，不过稀疏图一般都用Kruskal算法）：

```cpp
#include <bits/stdc++.h>
using namespace std;

int fi = []{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    return 0;
}();

int main() {
    int n;
    cin >> n;

    vector<vector<pair<int, int>>> adj(n);   // 邻接表：{邻居, 边权}
    for (int i = 0; i < n; ++i)
        for (int j = 0; j < n; ++j) {
            int w;
            cin >> w;
            if (i != j) adj[i].emplace_back(j, w);
        }

    // 小根堆：{边权, 点}
    priority_queue<pair<int, int>, vector<pair<int, int>>, greater<>> pq;
    vector<bool> in_mst(n, false);

    pq.emplace(0, 0);        // 从 0 号点开始，代价 0
    int cost = 0, cnt = 0;

    while (!pq.empty() && cnt < n) {
        auto [w, u] = pq.top();
        pq.pop();
        if (in_mst[u]) continue;   // 懒删除：过期条目直接跳过
        in_mst[u] = true;
        cost += w;
        ++cnt;
        for (auto [v, ew] : adj[u])
            if (!in_mst[v])
                pq.emplace(ew, v);
    }

    cout << cost;
}
```

