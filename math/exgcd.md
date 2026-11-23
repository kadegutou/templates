## exgcd

```cpp
int exgcd(int a, int b, int& x, int& y) {
    if (b == 0) {
        x = 1, y = 0;
        return a;
    } else {
        int g = exgcd(b, a % b, y, x);
        y -= (a / b) * x;
        return g;
    }
}

int floor_div(int a, int b) {
    if (a >= 0) return a / b;
    else return -((-a + b - 1) / b);
}

int ceil_div(int a, int b) {
    if (a >= 0) return (a + b - 1) / b;
    else return a / b;
}

int a, b, c, d, e, t, na, nb, x0, Y0, hi, lo;

signed main() {
    read(t);

    while (t--) {
        read(a); read(b); read(c);
        d = exgcd(a, b, x0, Y0);
        if (c % d != 0) printf("-1\n");
        else {
            na = a / d, nb = b / d;
            e = c / d, x0 *= e, Y0 *= e;
            lo = floor_div(-x0, nb) + 1, hi = ceil_div(Y0, na) - 1;

            if (hi >= lo) cout << hi - lo + 1 << ' '
                               << x0 + lo * nb << ' ' << Y0 - hi * na << ' '
                               << x0 + hi * nb << ' ' << Y0 - lo * na << '\n';
            else cout << x0 + lo * nb << ' ' << Y0 - hi * na << '\n';
        }
    }
}
```



## 取模避免溢出

```cpp
signed main() {
    read(n), read(p), read(w), read(d);
    int x0, y0;
    int g = exgcd(w, d, x0, y0);
    if (p % g != 0) {
        cout << -1 << '\n';
        return 0;
    }
    int mod = d / g;
    // 计算 x 的最小非负解
    int e = p / g;
    int x_base = ((x0 % mod + mod) % mod) * (e % mod) % mod;
    // 计算对应的 y
    int y_base = (p - w * x_base) / d;
    // 现在 x_base 是满足方程的最小非负 x（模 mod 意义下）
    // 但 y_base 可能为负，我们需要增加 x 使 y >= 0
    if (y_base < 0) {
        // 需要增加 x，即增加 k
        int step = w / g; // y 每增加一个 mod，y 减少 step
        int k = ceil_div(-y_base, step);
        x_base += mod * k;
        y_base -= step * k;
    }
    // 现在 y_base >= 0，且 x_base 是满足 y >= 0 的最小 x
    // 为了最小化 x+y，我们应该取最大的 x，即 y 最小的非负解
    int step_y = w / g;
    int k_max = y_base / step_y; // 可以增加的 k 的数量
    int x = x_base + mod * k_max;
    int y = y_base - step_y * k_max;
    if (x + y <= n) {
        cout << x << ' ' << y << ' ' << n - x - y << '\n';
    } else {
        cout << -1 << '\n';
    }
}
```

