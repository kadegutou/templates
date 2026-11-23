## 费马小定理 + 快速幂

适用条件：`p 是素数且 a 不是 p 的倍数`

```cpp
int fast_pow(int x, int e) {
    int res = 1;
    x %= MOD;
    while (e) {
        if (e & 1) res = res * x % MOD;
        x = x * x % MOD;
        e >>= 1;
    }
    
    return res;
}

int inv(int a, int p) {
    return fast_pow(a, p - 2) % p;
}

```



## exgcd：

适用条件：`a ，p 互素，即 gcd(a, p) = 1 ，不要求 p 为素数`

```cpp
int exgcd(int a, int p, int& x, int& y) {
    if (p == 0) {
        x = 1, y = 0;
        return a;
    } else {
        int g = exgcd(p, a % p, y, x);
        y -= (a / p) * x;
        return g;
    }
}

int inv(int a, int p) {
    int x, y;
    exgcd(a, p, x, y);
    x = (x % p + p) % p;
    return x == 0 ? p : x;
}
```



## 线性递推（批量求逆元）：

适用条件：`p 是质数，求 1..n 的所有逆元且 n < p`

```cpp
constexpr int MAXN;

int inv[MAXN];

void solve(int p) {
    inv[1] = 1;
    for (int i = 2; i < MAXN; ++i)
        inv[i] = (p - p / i) * inv[p % i] % p;
}
```

