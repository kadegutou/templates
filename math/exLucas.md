## exLucas

```cpp
constexpr int MAXN = 1'000'005;

int pre[MAXN], n, m, p;

int fast_pow(int a, int x, int mod) {
    int res = 1;
    while (x) {
        if (x & 1) res = res * a % mod;
        a = a * a % mod;
        x >>= 1;
    }

    return res;
}

// Legendre's formula（勒让德公式）
int legendre(int n, int p) {
    int cnt = 0;
    while (n) {
        n /= p;
        cnt += n;
    }

    return cnt;
}

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

void prev(int p, int pk) {
    pre[0] = 1;
    for (int i = 1; i <= pk; ++i) {
        if (i % p == 0) pre[i] = pre[i - 1];
        else pre[i] = pre[i - 1] * i % pk;
    }
}

int f(int n, int p, int pk) {
    if (n == 0) return 1;

    int res = 1;
    res = res * fast_pow(pre[pk], n / pk, pk) % pk * pre[n % pk] % pk * f(n / p, p, pk) % pk;

    return res;
}

int comb(int n, int m, int p, int pk, int k) {
    if (m > n) return 0;

    int exp = legendre(n, p) - legendre(n - m, p) - legendre(m, p);
    if (exp >= k) return 0;

    int res = fast_pow(p, exp, pk);
    res = res * f(n, p, pk) % pk * inv(f(m, p, pk), pk) % pk * inv(f(n - m, p, pk), pk) % pk;

    return (res + pk) % pk;
}

void excrt(int& r1, int r2, int& m1, int m2) {
    int l = m1 * m2;
    r1 = (r1 + inv(m1, m2) * m1 % l * (r2 - r1) % l + l) % l;
    m1 = l;
}

int solve() {
    vector<int> prime, exp;
    int temp = p;
    for (int i = 2; i * i <= temp; ++i) {
        if (temp % i == 0) {
            prime.emplace_back(i);
            exp.emplace_back(0);
            while (temp % i == 0) {
                temp /= i;
                ++exp.back();
            }
        }
    }
    if (temp > 1) {
        prime.emplace_back(temp);
        exp.emplace_back(1);
    }

    int ans = 0, mod = 1;
    for (int i = 0; i < prime.size(); ++i) {
        int x = prime[i], k = exp[i], pk = fast_pow(x, k, MAXN);
        prev(x, pk);
        int r = comb(n, m, x, pk, k);
        excrt(ans, r, mod, pk);
    }

    return ans;
}
```

