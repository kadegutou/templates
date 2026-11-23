## Trie（模板）：

```cpp
class TrieNode {
public:
    TrieNode* children[26];
    bool isEnd;
    
    TrieNode() {
        fill(children, children + 26, 0);
        isEnd = false;
    }
};

class Trie {
private:
    TrieNode* root;
    
public:
    Trie() {
        root = new TrieNode();
    }
    
    void insert(string& word) {
        TrieNode* node = root;
        for (auto& c : word) {
            int idx = c - 'a';
            if (!node->children[idx]) {
                node->children[idx] = new TrieNode();
            }
            node = node->children[idx];
        }
        node->isEnd = true;
    }
    
    bool search(string& word) {
        TrieNode* node = find(word);
        return node && node->isEnd;
    }
    
    bool startsWith(string prefix) {
        return find(prefix) != nullptr;
    }
    
private:
    TrieNode* find(string& s) {
        TrieNode* node = root;
        for (char& c : s) {
            int idx = c - 'a';
            if (!node->children[idx]) return nullptr;
            node = node->children[idx];
        }
        return node;
    }
};
```



## 另一套模板（洛谷8306）

```cpp
#include<bits/stdc++.h>
using namespace std;
int z = []{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    return 0;
}();

class Trie {
    // 0-25 小写  26-51 大写  52-61 数字
    static int idx(char c) {
        if (c >= 'a' && c <= 'z') return c - 'a';
        if (c >= 'A' && c <= 'Z') return c - 'A' + 26;
        return c - '0' + 52;  // 数字 0-9
    }

    vector<array<int, 62>> ch;
    vector<int> cnt;

public:
    Trie() {
        ch.push_back({});   // 根节点
        cnt.push_back(0);
    }

    void insert(const string& word) {
        int u = 0;
        for (char c : word) {
            int id = idx(c);
            if (!ch[u][id]) {
                ch.push_back({});
                cnt.push_back(0);
                ch[u][id] = ch.size() - 1;
            }
            u = ch[u][id];
            cnt[u]++;
        }
    }

    int query_prefix(const string& s) const {
        int u = 0;
        for (char c : s) {
            int id = idx(c);
            if (!ch[u][id]) return 0;
            u = ch[u][id];
        }
        return cnt[u];
    }
};

int main() {
    int t;
    cin >> t;
    while (t--) {
        int n, q;
        cin >> n >> q;
        Trie trie;
        string s;
        while (n--) {
            cin >> s;
            trie.insert(s);
        }
        while (q--) {
            cin >> s;
            cout << trie.query_prefix(s) << '\n';
        }
    }
    return 0;
}
```



## rust版：

```rust
struct TrieNode {
    children: [Option<Box<TrieNode>>; 26],
    is_end: bool,
}

impl TrieNode {
    fn new() -> Self {
        TrieNode {
            children: Default::default(),
            is_end: false,
        }
    }
}

struct Trie {
    root: TrieNode,
}

impl Trie {
    fn new() -> Self {
        Trie {
            root: TrieNode::new(),
        }
    }
    
    pub fn insert(&mut self, word: String) {
        let mut node = &mut self.root;
        for c in word.bytes() {
            let id = (c - b'a') as usize;
            if node.children[id].is_none() {
                node.children[id] = Some(Box::new(TrieNode::new()));
            }
            node = node.children[id].as_mut().unwrap();
        }
        node.is_end = true;
    }
    
    fn search(&self, word: String) -> bool {
        let mut node = &self.root;
        for c in word.bytes() {
            let id = (c - b'a') as usize;
            match &node.children[id] {
                Some(child) => node = child,
                None => return false,
            }
        }
        node.is_end
    }
    
    fn starts_with(&self, prefix: String) -> bool {
        let mut node = &self.root;
        for c in prefix.bytes() {
            let id = (c - b'a') as usize;
            match &node.children[id] {
                Some(child) => node = child,
                None => return false,
            }
        }
        true
    }
}
```

