## CRT

```cpp
#define int __int128
constexpr int MAXN = 15;

void exgcd(int a, int b, int& x, int& y) {
    if (b == 0) x = 1, y = 0;
    else {
        exgcd(b, a % b, y, x);
        y -= (a / b) * x;
    }
}

int inv(int a, int p) {
    int x, y;
    exgcd(a, p, x, y);
    x = (x % p + p) % p;
    return x;
}

int r[MAXN], m[MAXN], n, a, b, x = 1;

int crt() {
    int res = 0;
    for (int i = 1; i <= n; ++i) {
        int exm = x / m[i];
        res = (exm * inv(exm, m[i]) % x * r[i] % x + res) % x;
    }
    return res;
}

signed main() {
    read(n);
    
    for (int i = 1; i <= n; ++i) {
        read(a), read(b);
        x *= a;
        m[i] = a, r[i] = b;
    }
    
    cout << (long long)crt();
}
```



## EXCRT

```cpp
#define int __int128

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

int inv(int a, int p) {
    int x, y;
    exgcd(a, p, x, y);
    x = (x % p + p) % p;
    return x;
}

void excrt(int& r1, int r2, int& m1, int m2) {
    int x, y;
    int g = exgcd(m1, m2, x, y), l = m1 * m2 / g;
    
    // 判断 CRT 无解的条件：(r2 - r1) % g != 0
    // m1 = m1 * m2 / g;
    r1 = (r1 + m1 * inv(m1 / g, m2 / g) % l * ((r2 - r1) / g) % l + l) % l;
    m1 = l;
}

void print(int x) {
    if (x < 0) cout << '-';
    if (x > 9) print(x / 10);
    cout << '0' + (long long)x % 10;
}

int n, a, b, r, m;

signed main() {
    read(n), read(m), read(r);

    for (int i = 1; i < n; ++i) {
        read(a), read(b);
        excrt(r, b, m, a);
    }

    print(r);
}
```

