## 0 - 1背包（洛谷1048，1060）：

```cpp
#include <bits/stdc++.h>
using namespace std;

int fi = []{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    return 0;
}();

int n, m;
vector<int> w;
vector<int> v;
vector<int> dp;

int main() {
    cin >> n >> m;
    w.resize(m), v.resize(m);
    for (int i = 0; i < m; ++i) {
        cin >> w[i] >> v[i];
    }

    dp.resize(n + 1);
    for (int i = 0; i < m; ++i) {
        for (int j = n; j >= w[i]; --j) {
            dp[j] = max(dp[j], dp[j - w[i]] + v[i]);
        }
    }

    cout << dp[n];
}
```



## 二维背包（洛谷1855）：

```cpp
#include <bits/stdc++.h>
using namespace std;

int fi = []{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    return 0;
}();

int n, m, t;
vector<int> mo;
vector<int> ti;

int main() {
    cin >> n >> m >> t;
    mo.resize(n), ti.resize(n);
    vector<vector<int>> dp(m + 1, vector<int>(t + 1, 0));
    for (int i = 0; i < n; ++i) {
        cin >> mo[i] >> ti[i];
    }

    for (int i = 0; i < n; ++i) {
        for (int j = m; j >= mo[i]; --j) {
            for (int k = t; k >= ti[i]; --k) {
                dp[j][k] = max(dp[j][k], dp[j - mo[i]][k - ti[i]] + 1);
            }
        }
    }

    cout << dp[m][t];
}
```



## 多重背包 · 二进制拆分（1776）：

```cpp
#include <bits/stdc++.h>
using namespace std;

int fi = []{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    return 0;
}();

int n, W;
vector<int> w;
vector<int> v;
vector<int> m;			//加了一个维度每件物品的数量
vector<int> dp;

int main() {
    cin >> n >> W;
    w.resize(n), v.resize(n), m.resize(n);
    for (int i = 0; i < n; ++i) {
        cin >> v[i] >> w[i] >> m[i];
    }

    dp.resize(W + 1);
    for (int i = 0; i < n; ++i) {
        for (int c = 1; m[i] > 0; c <<= 1) {			//二进制拆分，1，2，4，8……
            int take = min(c, m[i]);
            m[i] -= take;

            for (int j = W; j >= w[i] * take; --j) {
                dp[j] = max(dp[j], dp[j - w[i] * take] + v[i] * take);
            }
        }
    }

    cout << dp[W];
}
```



## 分组背包（洛谷1064）：

```cpp
#include <bits/stdc++.h>
using namespace std;

int z = []{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    return 0;
}();

int n, m, v, w, q;
vector<vector<pair<int, int>>> group;
vector<tuple<int, int, int>> items;

int main() {
    cin >> n >> m;
    group.resize(m + 1);
    items.resize(m + 1);
    for (int i = 1; i <= m; ++i) {
        auto& [x, y, z] = items[i];
        cin >> x >> y >> z;
    }

    for (int i = 1; i <= m; ++i) {
        auto& [x, y, z] = items[i];
        if (z == 0) group[i].insert(group[i].begin(), {x, y});
        else group[z].emplace_back(x, y);
    }

    vector dp(n + 1, 0);
    for (auto& g : group) {
        if (g.empty()) continue;
        auto& [mv, mw] = g[0];
        vector<pair<int, int>> opt = {{mv, mw * mv}};

        if (g.size() >= 2) {
            auto& [mv1, mw1] = g[1];
            opt.emplace_back(mv + mv1, mw * mv + mv1 * mw1);
            
            if (g.size() >= 3) {
                auto& [mv2, mw2] = g[2];
                opt.emplace_back(mv + mv2, mw * mv + mv2 * mw2);
                opt.emplace_back(mv + mv1 + mv2, mw * mv + mv1 * mw1 + mv2 * mw2);
            }
        }

        for (int k = n; k >= 0; --k)
            for (auto& [cost, val] : opt)
                if (k >= cost)
                    dp[k] = max(dp[k], dp[k - cost] + val);
    }

    cout << dp[n];
}
```



## bitset 优化 0 - 1 背包（LC3181，3489，3685）

```CPP
class Solution {
public:
    int maxTotalReward(vector<int>& rewardValues) {
        int mx = ranges::max(rewardValues);

        unordered_set<int> st;
        for (int& x : rewardValues) {
            if (st.count(x)) continue;
            if (x == mx - 1 || st.count(mx - 1 - x)) return 2 * mx - 1;
            else st.insert(x);
        }

        ranges::sort(rewardValues);
        rewardValues.erase(unique(rewardValues.begin(), rewardValues.end()), rewardValues.end());
        bitset<100000> f{1};

        for (int& x : rewardValues) {
            int shift = f.size() - x;
            f |= f << shift >> shift - x;
        }

        for (int i = 2 * rewardValues.back() - 1; i >= 0; --i)
            if (f.test(i))
                return i;
        return 0;
    }
};
```

```RUST
use bitvec::prelude::*;

impl Solution {
    pub fn max_total_reward(mut reward_values: Vec<i32>) -> i32 {
        reward_values.sort_unstable();
        reward_values.dedup();
        let mx = *reward_values.last().unwrap_or(&0) as usize;
        let mut f = bitvec![usize, Lsb0; 0; 2 * mx];
        f.set(0, true);

        for &x in &reward_values {
            let x = x as usize;
            let shift = f.len() - x;
            f |= &f.clone() << shift >> shift - x;
        }

        f.iter().enumerate().rev().find(|(_, b)| **b).map(|(i, _)| i as i32).unwrap_or(0)
    }
}
```

