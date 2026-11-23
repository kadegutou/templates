## KMP 模板（洛谷3375）

```cpp
vector<int> getNext(string& s) {
        vector<int> next(s.size());
        int j = 0;  // 前缀末尾
        
        for (int i = 1; i < s.size(); i++) {
            while (j > 0 && s[i] != s[j])
                j = next[j - 1];  // 回退
            
            if (s[i] == s[j])
                j++;
            
            next[i] = j;  // 记录最长相等前后缀
        }
        
        return next;
    }
```



## KMP 应用：最短信息长度（洛谷4391）

```cpp
#include <bits/stdc++.h>
using namespace std;

vector<int> getnext(string& s) {
    int n = s.size();
    vector<int> next(n);
    int j = 0;
    
    for (int i = 1; i < n; ++i) {
        while (j > 0 && s[j] != s[i]) j = next[j - 1];

        if (s[j] == s[i]) ++j;

        next[i] = j;
    }

    return next;
}

int l;
string s;

int main() {
    cin >> l >> s;
    vector<int> next = getnext(s);
    cout << l - next[l - 1];
}
```

