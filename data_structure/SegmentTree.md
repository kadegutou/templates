## 普通线段树（模板元编程）：

```cpp
template<typename T>
class SegmentTree {
    int n;
    vector<T> tree;

    T merge_val(T a, T b) const {
        return max(a, b);
    }

    void maintain(int node) {
        tree[node] = merge_val(tree[node << 1], tree[node << 1 | 1]);
    }

    void build(const vector<T>& a, int node, int left, int right) {
        if (left == right) {
            tree[node] = a[left];
            return;
        }
        int mid = left + (right - left) / 2;
        build(a, node << 1, left, mid);
        build(a, node << 1 | 1, mid + 1, right);
        maintain(node);
    }

    void update(int node, int left, int right, int i, T val) {
        if (left == right) {
            tree[node] = val;
            return;
        }
        int mid = left + (right - left) / 2;
        if (i <= mid) update(node << 1, left, mid, i, val);
        else update(node << 1 | 1, mid + 1, right, i, val);
        maintain(node); 
    }

    T query(int node, int l, int r, int q_l, int q_r) {
        if (q_l > q_r) return 0;
        if (q_l <= l && r <= q_r) return tree[node];
        int m = (l + r) >> 1;
        int res = 0;
        if (q_l <= m) res += query(node << 1, l, m, q_l, q_r);
        if (q_r > m) res += query(node << 1 | 1, m + 1, r, q_l, q_r);
        return res;
    }

public:
    explicit SegmentTree(const vector<T>& a): n(a.size()), tree(4 * n) {
        build(a, 1, 0, n - 1);
    }

    void update(int i, int val) {
        update(1, 0, n - 1, i, val);
    }

    T query(int q_left, int q_right) const {
        return query(1, 0, n - 1, q_left, q_right);
    }

    T get(int i) const {
        return query(1, 0, n - 1, i, i);
    }
};
```

```rust
struct SegmentTree {
    n: usize,
    tree: Vec<i32>,
}

impl SegmentTree {
    fn build(&mut self, nums: &[i32], node: usize, l: usize, r: usize) -> () {
        if l == r {
            self.tree[node] = nums[l];
            return;
        }

        let m = l + (r - l) / 2;
        self.build(&nums, node << 1, l, m);
        self.build(&nums, node << 1 | 1, m + 1, r);
        self.tree[node] = std::cmp::max(self.tree[node << 1], self.tree[node << 1 | 1]);
    }

    fn update(&mut self, node: usize, l: usize, r: usize, i: usize, val: i32) -> () {
        if l == r {
            self.tree[node] = val;
            return;
        }

        let m = l + (r - l) / 2;
        if i <= m { self.update(node << 1, l, m, i, val); }
        else { self.update(node << 1 | 1, m + 1, r, i, val) };
        self.tree[node] = std::cmp::max(self.tree[node << 1], self.tree[node << 1 | 1]);
    }

    fn find_first(&self, node: usize, l: usize, r: usize, val: i32) -> Option<usize> {
        if self.tree[node] < val { return None; }
        if l == r { return Some(l); }

        let m = l + (r - l) / 2;
        self.find_first(node << 1, l, m, val).or_else(|| self.find_first(node << 1 | 1, m + 1, r, val))
    }

    pub fn new(nums: &[i32]) -> Self {
        let n = nums.len();
        let mut tree = vec![0; 4 * n];
        let mut seg = SegmentTree {n, tree };
        seg.build(&nums, 1, 0, n - 1);
        seg
    }

    fn update_pub(&mut self, i: usize, val: i32) -> () {
        self.update(1, 0, self.n - 1, i, val);
    }

    fn find_first_pub(&self, val: i32) -> Option<usize> {
        self.find_first(1, 0, self.n - 1, val)
    }
}

impl Solution {
    pub fn num_of_unplaced_fruits(fruits: Vec<i32>, baskets: Vec<i32>) -> i32 {
        let mut st = SegmentTree::new(&baskets);

        let mut ans = 0;
        for x in &fruits {
            if let Some(pos) = st.find_first_pub(*x) { st.update_pub(pos, 0); }
            else { ans += 1; }
        }

        ans
    }
}
```



## Lazy 线段树（洛谷3372，3373，2221）

```cpp
class SegmentTree {
    int n;
    vector<long long> tree, add, mul;

    void push_up(int node) {
        tree[node] = tree[node << 1] + tree[node << 1 | 1];
    }

    void push_down(int node, int l, int r) {
        int m = l + (r - l) / 2;
        int left_len = m - l + 1;
        int right_len = r - m;
        int left = node << 1;
        int right = node << 1 | 1;

        // 乘法标记下传
        if (mul[node] != 1) {
            tree[left] *= mul[node];
            mul[left] *= mul[node];
            add[left] *= mul[node];

            tree[right] *= mul[node];
            mul[right] *= mul[node];
            add[right] *= mul[node];

            mul[node] = 1;
        }

        // 加法标记下传
        if (add[node] != 0) {
            tree[left] += add[node] * left_len;
            add[left] += add[node];

            tree[right] += add[node] * right_len;
            add[right] += add[node];

            add[node] = 0;
        }
    }

    void build(int node, int l, int r, const vector<long long>& a) {
        if (l == r) {
            tree[node] = a[l];
            return;
        }
        int m = l + (r - l) / 2;
        build(node << 1, l, m, a);
        build(node << 1 | 1, m + 1, r, a);
        push_up(node);
    }

    void update_add(int node, int l, int r, int ql, int qr, long long val) {
        if (ql <= l && r <= qr) {
            tree[node] += val * (r - l + 1);
            add[node] += val;
            return;
        }
        push_down(node, l, r);
        int m = l + (r - l) / 2;
        if (ql <= m) update_add(node << 1, l, m, ql, qr, val);
        if (qr > m)  update_add(node << 1 | 1, m + 1, r, ql, qr, val);
        push_up(node);
    }

    void update_mul(int node, int l, int r, int ql, int qr, long long val) {
        if (ql <= l && r <= qr) {
            tree[node] *= val;
            mul[node] *= val;
            add[node] *= val;
            return;
        }
        push_down(node, l, r);
        int m = l + (r - l) / 2;
        if (ql <= m) update_mul(node << 1, l, m, ql, qr, val);
        if (qr > m)  update_mul(node << 1 | 1, m + 1, r, ql, qr, val);
        push_up(node);
    }

    long long query(int node, int l, int r, int ql, int qr) {
        if (ql <= l && r <= qr) return tree[node];
        push_down(node, l, r);
        int m = l + (r - l) / 2;
        long long res = 0;
        if (ql <= m) res += query(node << 1, l, m, ql, qr);
        if (qr > m)  res += query(node << 1 | 1, m + 1, r, ql, qr);
        return res;
    }

public:
    explicit SegmentTree(const vector<long long>& a)
        : n(a.size()), tree(4 * n), add(4 * n, 0), mul(4 * n, 1) {
        build(1, 0, n - 1, a);
    }

    void range_add(int l, int r, long long val) {
        if (l > r) return;
        update_add(1, 0, n - 1, l, r, val);
    }

    void range_mul(int l, int r, long long val) {
        if (l > r) return;
        update_mul(1, 0, n - 1, l, r, val);
    }

    long long range_sum(int l, int r) {
        if (l > r) return 0;
        return query(1, 0, n - 1, l, r);
    }
};
```





## 灵神版

```cpp
template<typename T, typename F>
class LazySegmentTree {
    // 注：也可以去掉 template<typename T, typename F>，改在这里定义 T 和 F
    // using T = pair<int, int>;
    // using F = pair<int, int>;

    // 懒标记初始值
    const F TODO_INIT = 0; // **根据题目修改**

    struct Node {
        T val;
        F todo;
    };

    int n;
    vector<Node> tree;

    // 合并两个 val
    T merge_val(const T& a, const T& b) const {
        return a + b; // **根据题目修改**
    }

    // 合并两个懒标记
    F merge_todo(const F& a, const F& b) const {
        return a + b; // **根据题目修改**
    }

    // 把懒标记作用到 node 子树（本例为区间加）
    void apply(int node, int l, int r, F todo) {
        Node& cur = tree[node];
        // 计算 tree[node] 区间的整体变化
        cur.val += todo * (r - l + 1); // **根据题目修改**
        cur.todo = merge_todo(todo, cur.todo);
    }

    // 把当前节点的懒标记下传给左右儿子
    void spread(int node, int l, int r) {
        Node& cur = tree[node];
        F todo = cur.todo;
        if (todo == TODO_INIT) { // 没有需要下传的信息
            return;
        }
        int m = (l + r) >> 1;
        apply(node * 2, l, m, todo);
        apply(node * 2 + 1, m + 1, r, todo);
        cur.todo = TODO_INIT; // 下传完毕
    }

    // 合并左右儿子的 val 到当前节点的 val
    void maintain(int node) {
        tree[node].val = merge_val(tree[node * 2].val, tree[node * 2 + 1].val);
    }

    // 用 a 初始化线段树
    // 时间复杂度 O(n)
    void build(const vector<T>& a, int node, int l, int r) {
        Node& cur = tree[node];
        cur.todo = TODO_INIT;
        if (l == r) { // 叶子
            cur.val = a[l]; // 初始化叶节点的值
            return;
        }
        int m = (l + r) >> 1;
        build(a, node * 2, l, m); // 初始化左子树
        build(a, node * 2 + 1, m + 1, r); // 初始化右子树
        maintain(node);
    }

    void update(int node, int l, int r, int ql, int qr, F f) {
        if (ql <= l && r <= qr) { // 当前子树完全在 [ql, qr] 内
            apply(node, l, r, f);
            return;
        }
        spread(node, l, r);
        int m = (l + r) >> 1;
        if (ql <= m) { // 更新左子树
            update(node * 2, l, m, ql, qr, f);
        }
        if (qr > m) { // 更新右子树
            update(node * 2 + 1, m + 1, r, ql, qr, f);
        }
        maintain(node);
    }

    T query(int node, int l, int r, int ql, int qr) {
        if (ql <= l && r <= qr) { // 当前子树完全在 [ql, qr] 内
            return tree[node].val;
        }
        spread(node, l, r);
        int m = (l + r) >> 1;
        if (qr <= m) { // [ql, qr] 与右子树无交集，仅需递归左子树
            return query(node * 2, l, m, ql, qr);
        }
        if (ql > m) { // [ql, qr] 与左子树无交集，仅需递归右子树
            return query(node * 2 + 1, m + 1, r, ql, qr);
        }
        // [ql, qr] 与左右子树均有交集，分别递归，然后合并结果
        T l_res = query(node * 2, l, m, ql, qr);
        T r_res = query(node * 2 + 1, m + 1, r, ql, qr);
        return merge_val(l_res, r_res);
    }

public:
    // 线段树维护一个长为 n 的数组（下标从 0 到 n-1），元素初始值为 init_val
    LazySegmentTree(int n, T init_val = 0) : LazySegmentTree(vector<T>(n, init_val)) {}

    // 线段树维护数组 a
    LazySegmentTree(const vector<T>& a) : n(a.size()), tree(2 << bit_width(a.size() - 1)) {
        build(a, 1, 0, n - 1);
    }

    // 用 f 更新 [ql, qr] 中的每个 a[i]
    // 0 <= ql <= qr <= n-1
    // 时间复杂度 O(log n)
    void update(int ql, int qr, F f) {
        update(1, 0, n - 1, ql, qr, f);
    }

    // 返回用 merge_val 合并所有 a[i] 的计算结果，其中 i 在闭区间 [ql, qr] 中
    // 0 <= ql <= qr <= n-1
    // 时间复杂度 O(log n)
    T query(int ql, int qr) {
        return query(1, 0, n - 1, ql, qr);
    }
};
```



## 势能线段树：

```cpp
class SegmentTree {
    int n;
    vector<long long> sum, maxv;

    void push_up(int node) {
        sum[node] = sum[node << 1] + sum[node << 1 | 1];
        maxv[node] = max(maxv[node << 1], maxv[node << 1 | 1]);
    }

    void build(int node, int l, int r, const vector<long long>& a) {
        if (l == r) {
            sum[node] = maxv[node] = a[l];
            return;
        }
        int m = l + (r - l) / 2;
        build(node << 1, l, m, a);
        build(node << 1 | 1, m + 1, r, a);
        push_up(node);
    }

    // 区间开方
    void update_sqrt(int node, int l, int r, int ql, int qr) {
        if (maxv[node] <= 1) return;   // 势能耗尽，剪枝
        if (l == r) {                  // 叶子节点，直接修改
            sum[node] = sqrt(sum[node]);
            maxv[node] = sum[node];
            return;
        }
        int m = l + (r - l) / 2;
        if (ql <= m) update_sqrt(node << 1, l, m, ql, qr);
        if (qr > m)  update_sqrt(node << 1 | 1, m + 1, r, ql, qr);
        push_up(node);
    }
    
    // 区间取模
    void update_mod(int node, int l, int r, int ql, int qr, long long val) {
        if (maxv[node] < val) return;
        if (l == r) {
            tree[node] %= val;
            maxv[node] = tree[node];
            return;
        }

        int m = l + (r - l) / 2;
        if (ql <= m) update_mod(node << 1, l, m, ql, qr, val);
        if (qr > m) update_mod(node << 1 | 1, m + 1, r, ql, qr, val);
        merge(node);
    }

    // 区间求和
    long long query(int node, int l, int r, int ql, int qr) {
        if (ql <= l && r <= qr) return sum[node];
        int m = l + (r - l) / 2;
        long long res = 0;
        if (ql <= m) res += query(node << 1, l, m, ql, qr);
        if (qr > m)  res += query(node << 1 | 1, m + 1, r, ql, qr);
        return res;
    }

public:
    explicit SegmentTree(const vector<long long>& a) : n(a.size()), sum(4 * n), maxv(4 * n) {
        build(1, 0, n - 1, a);
    }

    void update_sqrt(int l, int r) {
        update_sqrt(1, 0, n - 1, l, r);
    }
    
    void update_mod(int l, int r, int val) {
        update_mod(1, 0, n - 1, l, r, val);
    }

    long long query(int l, int r) {
        return query(1, 0, n - 1, l, r);
    }
};
```



## 复杂逻辑处理示例 —— 0 - 1 序列（洛谷2572）

```cpp
class SegmentTree {
    int n;

    vector<int> pre0, suf0, max0;
    vector<int> pre1, suf1, max1;
    vector<int> cover, rev;
    vector<int> total;

    void merge(int node, int l, int r) {
        int ls = node << 1, rs = node << 1 | 1;
        int m = l + (r - l) / 2;
        int l_len = m - l + 1, r_len = r - m;

        total[node] = total[ls] + total[rs];

        pre0[node] = pre0[ls];
        if (pre0[ls] == l_len) pre0[node] = pre0[rs] + l_len;
        suf0[node] = suf0[rs];
        if (suf0[rs] == r_len) suf0[node] = suf0[ls] + r_len;
        max0[node] = max({max0[ls], max0[rs], pre0[rs] + suf0[ls]});

        pre1[node] = pre1[ls];
        if (pre1[ls] == l_len) pre1[node] = pre1[rs] + l_len;
        suf1[node] = suf1[rs];
        if (suf1[rs] == r_len) suf1[node] = suf1[ls] + r_len;
        max1[node] = max({max1[ls], max1[rs], pre1[rs] + suf1[ls]});
    }

    void apply_cover(int node, int len, int val) {
        total[node] = val * len;

        if (val == 0) {
            pre0[node] = suf0[node] = max0[node] = len;
            pre1[node] = suf1[node] = max1[node] = 0;
        } else {
            pre0[node] = suf0[node] = max0[node] = 0;
            pre1[node] = suf1[node] = max1[node] = len;
        }

        cover[node] = val;
        rev[node] = 0;
    }

    void apply_rev(int node, int len) {
        swap(pre0[node], pre1[node]);
        swap(suf0[node], suf1[node]);
        swap(max0[node], max1[node]);
        total[node] = len - total[node];
        rev[node] ^= 1;
    }

    void push_down(int node, int l, int r) {
        int m = l + (r - l) / 2;
        int ls = node << 1, rs = node << 1 | 1;
        int l_len = m - l + 1, r_len = r - m;

        if (cover[node] != -1) {
            apply_cover(ls, l_len, cover[node]);
            apply_cover(rs, r_len, cover[node]);
            cover[node] = -1;
        }

        if (rev[node]) {
            apply_rev(ls, l_len);
            apply_rev(rs, r_len);
            rev[node] = 0;
        }
    }

    void build(int node, int l, int r, const vector<int>& a) {
        if (l == r) {
            total[node] = a[l];
            
            if (total[node] == 0) {
                pre0[node] = suf0[node] = max0[node] = 1;
                pre1[node] = suf1[node] = max1[node] = 0;
            } else {
                pre0[node] = suf0[node] = max0[node] = 0;
                pre1[node] = suf1[node] = max1[node] = 1;
            }

            return;
        }

        int m = l + (r - l) / 2;
        build(node << 1, l, m, a);
        build(node << 1 | 1, m + 1, r, a);
        merge(node, l, r);
    }

    void update_cover(int node, int l, int r, int ql, int qr, int val) {
        if (ql <= l && r <= qr) {
            apply_cover(node, r - l + 1, val);
            return;
        }
        push_down(node, l, r);

        int m = l + (r - l) / 2;
        if (ql <= m) update_cover(node << 1, l, m, ql, qr, val);
        if (qr > m) update_cover(node << 1 | 1, m + 1, r, ql, qr, val);
        merge(node, l, r);
    }

    void update_rev(int node, int l, int r, int ql, int qr) {
        if (ql <= l && r <= qr) {
            apply_rev(node, r - l + 1);
            return;
        }
        push_down(node, l, r);

        int m = l + (r - l) / 2;
        if (ql <= m) update_rev(node << 1, l, m, ql, qr);
        if (qr > m) update_rev(node << 1 | 1, m + 1, r, ql, qr);
        merge(node, l, r);
    }

    int query_total(int node, int l, int r, int ql, int qr) {
        if (ql <= l && r <= qr) return total[node];
        push_down(node, l, r);

        int m = l + (r - l) / 2;
        int res = 0;
        if (ql <= m) res += query_total(node << 1, l, m, ql, qr);
        if (qr > m) res += query_total(node << 1 | 1, m + 1, r, ql, qr);
        return res;
    }

    void query_cnt(int node, int l, int r, int ql, int qr, int& res_pre, int& res_suf, int& res_max, int& res_len) {
        if (ql <= l && r <= qr) {
            res_pre = pre1[node];
            res_suf = suf1[node];
            res_max = max1[node];
            res_len = r - l + 1;
            return;
        }
        push_down(node, l, r);

        int m = l + (r - l) / 2;
        res_pre = res_suf = res_max = res_len = 0;

        if (ql <= m) {
            int l_pre, l_suf, l_max, l_len;
            query_cnt(node << 1, l, m, ql, qr, l_pre, l_suf, l_max, l_len);
            res_pre = l_pre;
            res_suf = l_suf;
            res_max = l_max;
            res_len = l_len;
        }
        if (qr > m) {
            int r_pre, r_suf, r_max, r_len;
            query_cnt(node << 1 | 1, m + 1, r, ql, qr, r_pre, r_suf, r_max, r_len);

            if (res_len == 0) {
                res_pre = r_pre, res_suf = r_suf, res_max = r_max, res_len = r_len;
            } else {
                int merge_len = r_len + res_len;

                int merge_pre = res_pre;
                if (r_pre == r_len) merge_pre = res_len + r_pre;
                int merge_suf = r_suf;
                if (r_suf == r_len) merge_suf = r_len + res_suf;

                res_pre = merge_pre;
                res_suf = merge_suf;
                res_len = merge_len;
                res_max = max({merge_pre, merge_suf, res_suf + r_pre});
            }
        }
    }

public:
    explicit SegmentTree(const vector<int>& a): n(a.size()),
                                                pre0(4 * n), suf0(4 * n), max0(4 * n),
                                                pre1(4 * n), suf1(4 * n), max1(4 * n),
                                                cover(4 * n, -1), rev(4 * n),
                                                total(4 * n) {
        build(1, 0, n - 1, a);
    }

    void update_cover(int l, int r, int val) {
        update_cover(1, 0, n - 1, l, r, val);
    }

    void update_rev(int l, int r) {
        update_rev(1, 0, n - 1, l, r);
    }

    int query_total(int l, int r) {
        return query_total(1, 0, n - 1, l, r);
    }

    int query_cnt(int l, int r) {
        int pre, suf, mx, len;
        query_cnt(1, 0, n - 1, l, r, pre, suf, mx, len);
        return mx;
    }
};
```

