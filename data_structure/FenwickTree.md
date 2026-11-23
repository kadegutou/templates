## 普通树状数组（LC307，洛谷3374）：

```cpp
// 根据题目用 FenwickTree<int> t(n) 或者 FenwickTree<long long> t(n) 初始化
template<typename T>
class FenwickTree {
    vector<T> tree;

public:
    // 使用下标 1 到 n
    FenwickTree(int n) : tree(n + 1) {}

    // a[i] 增加 val
    // 1 <= i <= n
    // 时间复杂度 O(log n)
    void update(int i, T val) {
        for (; i < tree.size(); i += i & -i) {
            tree[i] += val;
        }
    }

    // 求前缀和 a[1] + ... + a[i]
    // 1 <= i <= n
    // 时间复杂度 O(log n)
    T pre(int i) const {
        T res{};
        for (; i > 0; i &= i - 1) {
            res += tree[i];
        }
        return res;
    }

    // 求区间和 a[l] + ... + a[r]
    // 1 <= l <= r <= n
    // 时间复杂度 O(log n)
    T query(int l, int r) const {
        if (r < l) {
            return 0;
        }
        return pre(r) - pre(l - 1);
    }
};
```

```rust
struct NumArray {
    n: usize,
    nums: Vec<i32>,   // 原数组，保存当前的实际值
    tree: Vec<i32>,   // 树状数组，大小 n+1，索引从 1 开始
}
impl NumArray {
    fn new(nums: Vec<i32>) -> Self {
        let n = nums.len();
        let mut tree = vec![0; n + 1];

        for i in 1..=n {
            tree[i] += nums[i - 1];
            let nxt = i + (i & i.wrapping_neg());	// nxt = i + lowbit(i)
            if nxt <= n { tree[nxt] += tree[i]; }
        }
        NumArray { n, nums, tree }
    }
    
    fn update(&mut self, index: i32, val: i32) {
        let index = index as usize;
        let delta = val - self.nums[index];  // 计算变化量
        self.nums[index] = val;              // 更新原数组
        let mut i = index + 1;               // 树状数组索引从 1 开始
        while i <= self.n {
            self.tree[i] += delta;
            i += i & i.wrapping_neg();       // 跳到父节点 i + lowbit(i)
        }
    }

    fn prefix(&self, i: i32) -> i32 {
        let mut s = 0;
        let mut i = i as usize;
        while i > 0 {
            s += self.tree[i];
            i &= i - 1;
        }
        s
    }
    
    fn sum_range(&self, left: i32, right: i32) -> i32 {
        self.prefix(right + 1) - self.prefix(left)
    }
}
```



## 区间加 + 单点查询（洛谷3368）：

```cpp
#include<bits/stdc++.h>
using namespace std;

template<typename T>
inline void read(T &x) {
    x = 0; int f = 1; int ch = getchar();
    while (ch != EOF && !isdigit(ch)) { if (ch == '-') f = -1; ch = getchar(); }
    while (ch != EOF && isdigit(ch)) { x = x * 10 + ch - '0'; ch = getchar(); }
    x *= f;
}

class FenwickTree {
    vector<int> tree;

public:
    FenwickTree(int n): tree(n + 1) {}

    void update(int i, int x) {
        for (; i < tree.size(); i += i & -i)
            tree[i] += x;
    }

    int pre(int i) {
        int s = 0;
        for (; i > 0; i &= i - 1) s += tree[i];
        return s;
    }

    int query(int left, int right) {
        if (left > right) return 0;
        return pre(right) - pre(left - 1);
    }
};

int n, m, x, l, r, k;

int main() {
    read(n); read(m);
    FenwickTree bit(n + 2);

    int prev = 0;
    for (int i = 1; i <= n; ++i) {
        read(x);
        bit.update(i, x - prev);
        prev = x;
    }

    for (int i = 1; i <= m; ++i) {
        read(x);
        if (x == 1) {
            read(l); read(r); read(k);
            bit.update(l, k);
            bit.update(r + 1, -k);
        } else if (x == 2) {
            read(x);
            cout << bit.pre(x) << '\n';
        }
    }
}
```



## 区间加 + 区间查询：

```cpp
class FenwickRange {
    vector<long long> bit1, bit2;
    int n;

    void add(vector<long long>& bit, int idx, long long val) {
        for (; idx <= n; idx += idx & -idx)
            bit[idx] += val;
    }

    long long query(const vector<long long>& bit, int idx) {
        long long res = 0;
        for (; idx > 0; idx &= idx - 1)
            res += bit[idx];
        return res;
    }

    long long prefix(int x) {
        long long s1 = query(bit1, x);
        long long s2 = query(bit2, x);
        return (x + 1) * s1 - s2;
    }

public:
    FenwickRange(int n) : n(n), bit1(n + 1), bit2(n + 1) {}

    // 区间 [l, r] 加 val (l, r 从 1 开始)
    void range_add(int l, int r, long long val) {
        add(bit1, l, val);
        add(bit1, r + 1, -val);
        add(bit2, l, l * val);
        add(bit2, r + 1, -(r + 1) * val);
    }

    // 查询区间和 [l, r]
    long long range_sum(int l, int r) {
        return prefix(r) - prefix(l - 1);
    }
};
```



## 值域树状数组（LC3962，315，3072，3624，洛谷1908，CF61E）：

```cpp
class FenwickTree {
    const int high_bit;
    const vector<int>& sorted;
    vector<int> cnt;
    vector<long long> sum;

public:
    FenwickTree(const vector<int>& sorted) : 
        cnt(sorted.size() + 1), 
        sum(sorted.size() + 1), 
        sorted(sorted), 
        high_bit(1 << (bit_width(sorted.size()) - 1)) {}

    // 添加 num 个 val，其中 val 离散化后的值为 i（i 从 1 开始）
    // 如果 num < 0，表示减少 -num 个 val
    void update(int i, int num, int val) {
        for (; i < cnt.size(); i += i & -i) {
            cnt[i] += num;
            sum[i] += 1LL * num * val;
        }
    }

    // 返回第 k 小的数（k 从 1 开始）
    int kth(int k) const {
        int i = 0;
        for (int b = high_bit; b > 0; b >>= 1) {
            int nxt = i | b;
            if (nxt < cnt.size() && cnt[nxt] < k) {
                k -= cnt[nxt];
                i = nxt;
            }
        }
        return sorted[i];
    }

    // 返回前 k 小的数之和（k 从 1 开始）
    long long pre_sum(int k) const {
        long long s = 0;
        int i = 0;
        for (int b = high_bit; b > 0; b >>= 1) {
            int nxt = i | b;
            if (nxt < cnt.size() && cnt[nxt] < k) {
                k -= cnt[nxt];
                s += sum[nxt];
                i = nxt;
            }
        }
        // 加上等于第 k 小的数
        return s + 1LL * sorted[i] * k;;
    }
    
    // 求逆序对
    int query(int i) {
        int res = 0;
        for (; i > 0; i &= i - 1) res += cnt[i];
        return res;
    }
};
```

```rust
impl Solution {
    pub fn count_smaller(nums: Vec<i32>) -> Vec<i32> {
        let n = nums.len();
        let mut arr = nums.clone();
        arr.sort_unstable();
        arr.dedup();

        let mut cnt = vec![0; arr.len() + 1];

        fn get_id(x: i32, arr: &[i32]) -> usize {
            arr.binary_search(&x).unwrap() + 1
        }

        fn update(mut i: usize, cnt: &mut [i32]) -> () {
            while i < cnt.len() {
                cnt[i] += 1;
                i += i & i.wrapping_neg();
            }
        }

        fn query(mut i: usize, cnt: &[i32]) -> i32 {
            let mut s = 0;
            while i > 0 {
                s += cnt[i];
                i &= i - 1;
            }
            s
        }

        let mut ans = vec![0; n];
        for i in (0..n).rev() {
            let id = get_id(nums[i], &arr);
            ans[i] += query(id - 1, &cnt);
            update(id, &mut cnt);
        }

        ans
    }
}
```

