## 快读模板（大多数情况够用，整数版）：

```cpp
template<typename T>
inline void read(T &x) {
    x = 0; int f = 1; int ch = getchar();
    while (ch != EOF && !isdigit(ch)) { if (ch == '-') f = -1; ch = getchar(); }
    while (ch != EOF && isdigit(ch)) { x = x * 10 + ch - '0'; ch = getchar(); }
    x *= f;
}
```



## 安全版本：

```cpp
template<typename T>
inline bool read(T &x) {   // 返回是否成功读取一个数
    x = 0; int f = 1, ch = getchar();
    while (ch != EOF && !isdigit(ch)) {
        if (ch == '-') f = -1;
        ch = getchar();
    }
    if (ch == EOF) return false;   // 没读到任何数字
    while (isdigit(ch)) {
        x = x * 10 + (ch - '0');
        ch = getchar();
    }
    x *= f;
    return true;
}
```



## 字符串快读：

```cpp
template<typename T>
inline void read(std::string &s) {
    s.clear();
    int ch = getchar();
    // 跳过前导空白
    while (ch != EOF && isspace(ch)) ch = getchar();
    // 读取非空白字符
    while (ch != EOF && !isspace(ch)) {
        s.push_back(static_cast<char>(ch));
        ch = getchar();
    }
    // 结束后 ch 停留在结束的那个空白字符或 EOF
    // 如果是空白，可以放回缓冲区，以便下一次读取能正确跳过空白
    if (ch != EOF) ungetc(ch, stdin);
}

// 如果要读整行
inline void read_line(std::string &s) {
    s.clear();
    int ch = getchar();
    // 读入直到换行符 '\n' 或 EOF
    while (ch != EOF && ch != '\n') {
        s.push_back(static_cast<char>(ch));
        ch = getchar();
    }
    // 可选：吃掉换行符但不存入 s
    // 如果还想在之后读下一行，这里不需要放回
}
```



## 完整版：

```cpp
template<typename T>
inline void read(T &x) {
    if constexpr (is_same_v<T, string>) {
        // 字符串分支
        x.clear();
        int ch = getchar();
        while (ch != EOF && isspace(ch)) ch = getchar();
        while (ch != EOF && !isspace(ch)) {
            x.push_back(ch);
            ch = getchar();
        }
        if (ch != EOF) ungetc(ch, stdin);
    } else {
        // 整数分支
        x = 0; int f = 1, ch = getchar();
        while (ch != EOF && !isdigit(ch)) {
            if (ch == '-') f = -1;
            ch = getchar();
        }
        while (ch != EOF && isdigit(ch)) {
            x = x * 10 + (ch - '0');
            ch = getchar();
        }
        x *= f;
    }
}
```



## 整数快写：

```cpp
// 输出单个字符
inline void putc(char c) {
    putchar(c);
}

// 输出整数（支持 int, long long 等）
template<typename T>
inline void write(T x) {
    if (x < 0) {
        putc('-');
        x = -x;
    }
    if (x > 9) write(x / 10);
    putc(x % 10 + '0');
}

// 输出整数并换行
template<typename T>
inline void writeln(T x) {
    write(x);
    putc('\n');
}

// 输出整数并加空格
template<typename T>
inline void writesp(T x) {
    write(x);
    putc(' ');
}
```



## 字符串快写（重载）：

```cpp
// 输出 C 风格字符串
inline void write(const char* s) {
    while (*s) putc(*s++);
}

// 输出 std::string
inline void write(const std::string& s) {
    for (char c : s) putc(c);
}

// 字符串换行/空格版本同理
inline void writeln(const std::string& s) { write(s); putc('\n'); }
inline void writesp(const std::string& s) { write(s); putc(' '); }
```

