## Kahn算法

```cpp
queue<int> q;
vector<int> edge[MAXN];
int indegree[MAXN];

void kahn(int n) {
    for (int i = 1; i <= n; ++i)
        if (indegree[i] == 0)
            q.emplace(i);
    
    while (!q.empty()) {
        int u = q.front(); q.pop();
        
        for (int& v : edge[u]) {
            if (--indegree[v] == 0) {
                q.emplace(v);
            }
        }
    }
}
```





## 拓扑序（计数和DP，洛谷4017，3387，2272）

```cpp
#include <bits/stdc++.h>
using namespace std;

int fi = []{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    return 0;
}();

const int MOD = 80112002;
const int MAXN = 5e3 + 5;
int indegree[MAXN];
vector<int> e[MAXN];
int n, m, u, v;
queue<int> q;


int main() {
    cin >> n >> m;
    for (int i = 0; i < m; ++i) {
        cin >> u >> v;
        ++indegree[u];
        e[v].emplace_back(u);
    }

    vector dp(n + 1, 0);			//从某个顶级捕食者出发、以 i 为终点的食物链数量
    for (int i = 1; i <= n; ++i) {
        if (indegree[i] == 0) {
            dp[i] = 1;
            q.emplace(i);
        }
    }

    while (!q.empty()) {
        v = q.front(); q.pop();

        for (int& u : e[v]) {
            dp[u] = (dp[u] + dp[v]) % MOD;
            if (--indegree[u] == 0) q.emplace(u);
        }
    }

    int ans = 0;
    for (int i = 1; i <= n; ++i)
        if (e[i].empty())
            ans = (ans + dp[i]) % MOD;

    cout << ans << '\n';
}
```



## 拓扑序（复杂类别字符串输出，洛谷1347）

```cpp
#include <bits/stdc++.h>
using namespace std;

int fi = []{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    return 0;
}();

const int SIGMA = 26;
int n, m, u, v;
string s;
char x;

vector<int> e[SIGMA];
vector indegree(SIGMA, 0);
vector copy_indegree(SIGMA, 0);

int kahn(string& order) {
    queue<int> q;
    bool uniq = true;
    int cnt = 0;
    copy_indegree = indegree;
    for (int i = 0; i < n; ++i)			// 题目只用了前 n 个大写字母，把没用到的字母当入度 0 入队会污染计数
        if (copy_indegree[i] == 0)
            q.emplace(i);
    
    while (!q.empty()) {
        if (q.size() > 1) uniq = false;			// 队列里同时存在多个点，说明它们之间没有先后约束，谁先被弹都行——拓扑序不唯一。注意这只标记不唯一，不影响继续跑
        v = q.front(); q.pop();
        order += static_cast<char>(v + 'A');		// 弹出顺序就是候选答案
        ++cnt;			// 信号 2：处理了多少个点，正常 DAG 上每个点都会恰好入队一次；如果图里有环，环上的点入度永远减不到 0，永远进不了队 → cnt < n

        for (int& u : e[v])
            if (--copy_indegree[u] == 0)
                q.emplace(u);
    }

    if (cnt < n) return 2;			// 有环 = 矛盾
    return uniq ? 1 : 0;			// 无环：唯一 → 1，不唯一 → 0
}

int main() {
    cin >> n >> m;
    for (int i = 1; i <= m; ++i) {
        cin >> s;
        u = s[0] - 'A', x = s[1], v = s[2] - 'A';
        if (x == '<') swap(u, v);
        e[v].emplace_back(u);
        ++indegree[u];

        string order;
        int st = kahn(order);
        if (st == 2) {
            cout << "Inconsistency found after " << i << " relations.\n";
            return 0;
        } else if (st == 1) {
            cout << "Sorted sequence determined after " << i << " relations: " << order << ".\n";
            return 0;
        }
    }

    cout << "Sorted sequence cannot be determined.\n";
}
```



## 多状态（洛谷1038，我觉得有点问题）

```cpp
#include <bits/stdc++.h>
using namespace std;

int fi = []{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    return 0;
}();

const int MAXN = 1e2 + 5;
int n, p, u, v, a, b, w;

vector<pair<int, int>> e[MAXN];
int net[MAXN];
int indegree[MAXN];
queue<int> q;

void kahn() {
    while (!q.empty()) {
        u = q.front(); q.pop();
        if (net[u] <= 0) continue;			// 实际上的话就算net[u] <= 0，也应该执行删边的操作，但是这样修改的代码反而错了。这一版的代码虽然通过了，但是逻辑还是有一些问题的

        for (auto& [v, w] : e[u]) {
            net[v] += w * net[u];
            if (--indegree[v] == 0 && net[v] > 0) q.emplace(v);
        }
    }

    bool ok = false;
    for (int i = 1; i <= n; ++i) {
        if (net[i] > 0 && e[i].empty()) {
            ok = true;
            cout << i << ' ' << net[i] << '\n';
        }
    }

    if (ok) return;
    else cout << "NULL";
}

int main() {
    cin >> n >> p;
    for (int i = 1; i <= n; ++i) {
        cin >> a >> b;
        if (a > 0) {
            net[i] = a;
            q.emplace(i);
        } else net[i] -= b;
    }
    for (int i = 0; i < p; ++i) {
        cin >> u >> v >> w;
        e[u].emplace_back(v, w);
        ++indegree[v];
    }
    kahn();
}
```





## 字符串拓扑（LC2115）：

```python
class Solution:
    def findAllRecipes(self, recipes: List[str], ingredients: List[List[str]], supplies: List[str]) -> List[str]:
        g = defaultdict(list)
        deg = {}
        for r, ing in zip(recipes, ingredients):
            for s in ing:
                g[s].append(r)  # 从这道菜的原材料向这道菜连边
            deg[r] = len(ing)
        ans = []
        q = deque(supplies)  # 拓扑排序（用初始原材料当队列）
        while q:
            for r in g[q.popleft()]:
                deg[r] -= 1
                if deg[r] == 0:  # 这道菜的所有原材料我们都有
                    q.append(r)
                    ans.append(r)
        return ans
```

```go
func findAllRecipes(recipes []string, ingredients [][]string, q []string) (ans []string) {
	g := map[string][]string{}
	deg := make(map[string]int, len(recipes))
	for i, r := range recipes {
		for _, s := range ingredients[i] {
			g[s] = append(g[s], r) // 从这道菜的原材料向这道菜连边
		}
		deg[r] = len(ingredients[i])
	}
	for len(q) > 0 { // 拓扑排序（这里我们直接用初始原材料当队列）
		s := q[0]
		q = q[1:]
		for _, r := range g[s] {
			if deg[r]--; deg[r] == 0 { // 这道菜的所有原材料我们都有
				q = append(q, r)
				ans = append(ans, r)
			}
		}
	}
	return
}
```

```cpp
class Solution {
public:
    vector<string> findAllRecipes(vector<string>& recipes, vector<vector<string>>& ingredients, vector<string>& supplies) {
        unordered_set<string> st(supplies.begin(), supplies.end());
        unordered_map<string, vector<int>> mp;
        int n = recipes.size();
        vector<int> indegree(n, 0);

        for (int i = 0; i < n; ++i) {
            for (auto& s : ingredients[i]) {
                if (!st.count(s)) ++indegree[i];
                mp[s].emplace_back(i);
            }
        }

        queue<int> q;
        vector<string> path;
        for (int i = 0; i < n; ++i)
            if (indegree[i] == 0) {
                q.emplace(i);
                path.emplace_back(recipes[i]);
                st.insert(recipes[i]);
            }

        while (!q.empty()) {
            auto u = q.front(); q.pop();

            for (auto& v : mp[recipes[u]]) {
                if (--indegree[v] == 0) {
                    q.emplace(v);
                    path.emplace_back(recipes[v]);
                }
            }
        }

        return path;
    }
};
```



## ps：有时讨论一张图的出度会很困难，可以考虑建反向图，参考LC802