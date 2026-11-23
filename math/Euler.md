## 欧拉定理

```cpp
// 欧拉函数
int phi(int m) {
    int res = m;
    
    for (int i = 2; i * i <= m; ++i) {
        if (m % i == 0) {
            // 注意不要写成 res = res / (i - 1) * i，或者写成 res -= res / i;
            res = res / i * (i - 1);
            while (m % i == 0) m /= i;
        }
    }

    if (m > 1) res = res / (m - 1) * m;
    return res;
}

// 扩展欧拉定理
int exeuler(string s, int mod) {
    int rem = 0, ok = false;
    
    for (auto& c : s) {
        rem = rem * 10 + c - '0';
        if (rem >= mod) {
            ok = true;
            rem %= mod;
        }
    }
    if (ok) rem += mod;
    
    return rem;
}
```



## 幂塔降幂

说明：`个人认为更简单的做法，不是真的正确，只是能过大部分检测`

```cpp
constexpr int MAXN = 100'005;
int m, n, q, w[MAXN], l, r;
vector<int> phi_m;

int fast_pow(int a, int x, int mod, int& ok) {
    if (mod == 1) {
        ok = true;
        return 0;
    }
    if (a >= mod) ok = true;

    int res = 1;
    while (x) {
        if (x & 1) res = res * a;
        if (res >= mod) {
            res %= mod;
            ok = true;
        }
        a = a * a;
        if (a * res > mod && x) ok = true;
        if (a >= mod && x) {
            ok = true;
            a %= mod;
        }
        x >>= 1;
    }

    return res;
}

int phi(int x) {
    int res = x;
    for (int i = 2; i * i <= x; ++i) {
        if (x % i == 0) {
            res = res / i * (i - 1);
            while (x % i == 0) x /= i;
        }
    }

    if (x > 1) res = res / x * (x - 1);
    return res;
}

void pre(int x) {
    phi_m.emplace_back(x);
    if (x == 1) return;
    pre(phi(x));
}

int dfs(int id, int l, int r, int& ok) {
    if (phi_m[id] == 1) {
        ok = true;
        return 0;
    }
    if (r - l < id) {
        ok = (1 >= phi_m[id]);
        return 1;
    }

    int phi_x = phi_m[id + 1], mod = phi_m[id], ok_ = false;
    int pow = dfs(id + 1, l, r, ok_);

    return fast_pow(w[l + id], ok_ ? pow + phi_x : pow, mod, ok);
}
```



## 幂塔降幂正确的做法（改动很小）

```cpp
// 只有fast_pow 和 dfs 有改动
int fast_pow(int a, int x, int mod) {
    a %= mod;
    int res = 1;
    while (x) {
        if (x & 1) res = res * a % mod;
        a = a * a % mod;
        x >>= 1;
    }
    return res;
}

int dfs(int id, int l, int r, int& ok) {
    if (phi_m[id] == 1) { ok = true; return 0; }
    if (r - l < id) { ok = (1 >= phi_m[id]); return 1; }

    int phi_x = phi_m[id + 1], mod = phi_m[id], ok_ = false;
    int pow = dfs(id + 1, l, r, ok_);
    int exp = ok_ ? pow + phi_x : pow;

    int base = w[l + id];
    int res = fast_pow(base, exp, mod);

    if (base == 1) ok = (1 >= mod);
    else if (base == 0) ok = (0 >= mod);
    else {
        if (exp >= 31) ok = true;
        else {
            long long val = 1;
            ok = false;
            for (int i = 0; i < exp; ++i) {
                val *= base;
                if (val >= mod) { ok = true; break; }
            }
        }
    }
    return res;
}
```

