## 埃氏筛：

```cpp
vector<int> prime;
vector<bool> is_prime(MAXN, true);

void Eratosthenes(int n) {
    is_prime[1] = is_prime[0] = false;
	for (int i = 2; i * i <= n; ++i) {
    	if (is_prime[i]) {
        	for (int p = i * i; p <= n; p += i) {
            	is_prime[p] = false;
        	}
    	}
	}
    for (int i = 2; i <= n; ++i)
        if (is_prime[i])
            prime.emplace_back(i);
}
```



## 线性筛：

```cpp
vector<int> prime;
vector<bool> is_prime(MAXN, true);

void pre(int n) {
    is_prime[1] = is_prime[0] = false;
    for (int i = 2; i <= n; ++i) {
        if (is_prime[i]) prime.emplace_back(i);

        for (int j = 0; j < prime.size() && i * prime[j] <= n; ++j) {
            is_prime[i * prime[j]] = false;
            if (i % prime[j] == 0) break;
        }
    }
}
```



## 线性筛扩展版（同筛 phi / mu / 最小质因子）

```cpp
vector<int> prime, phi(MAXN), mu(MAXN), spf(MAXN);
vector<bool> is_prime(MAXN, true);

void pre(int n) {
    is_prime[1] = is_prime[0] = false;
    phi[1] = 1, mu[1] = 1;
    for (int i = 2; i <= n; ++i) {
        if (is_prime[i]) {
            prime.emplace_back(i);
            phi[i] = i - 1;
            mu[i] = -1;
            spf[i] = i;
        }

        for (int j = 0; j < prime.size() && i * prime[j] <= n; ++j) {
            int p = prime[j];
            is_prime[i * p] = false;
            spf[i * p] = p;

            if (i % p == 0) {
                phi[i * p] = phi[i] * p;    // p | i，指数 +1，phi 只多乘一个 p
                mu[i * p] = 0;              // 含平方因子
                break;
            }
            phi[i * p] = phi[i] * (p - 1);  // p 是新质因子
            mu[i * p] = -mu[i];
        }
    }
}
```

要点：

- `p | i` 时 `break` 是线性筛的核心，保证每个合数只被最小质因子筛掉一次，复杂度 O(n)
- `spf[x]` 是最小质因子，配合 `x / spf[x]` 可以 O(log x) 分解质因数
- `phi[x]` 是欧拉函数，`mu[x]` 是莫比乌斯函数（无平方因子为 (-1)^k，否则为 0）

