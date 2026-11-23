# ACM Templates

个人整理与维护的 ACM / ICPC / CCPC 竞赛算法模板库，以 C++ 实现为主，每个模板均标注对应的验证题目（洛谷 / Codeforces / LeetCode），便于直接套用与刷题对照。

## 目录结构

```
templates/
├── DP/               动态规划
├── data_structure/   数据结构
├── graph/            图论
├── math/             数学
├── string/           字符串
└── misc/             杂项
```

## 模板一览

### 动态规划（DP）

| 文件 | 内容 |
| --- | --- |
| [knapsack.md](DP/knapsack.md) | 0-1 背包、二维背包、多重背包（二进制拆分）、分组背包、bitset 优化背包 |
| [LIS and LCS.md](DP/LIS%20and%20LCS.md) | LIS、Dilworth 定理、LCS 转 LIS |

### 数据结构（data_structure）

| 文件 | 内容 |
| --- | --- |
| [FenwickTree.md](data_structure/FenwickTree.md) | 树状数组：单点加/区间查询、区间加/单点查询、区间加/区间查询、值域树状数组 |
| [SegmentTree.md](data_structure/SegmentTree.md) | 线段树：模板元编程版、Lazy 线段树、势能线段树、复杂标记处理（0-1 序列） |
| [SparseTable.md](data_structure/SparseTable.md) | 一维 ST 表 |

### 图论（graph）

| 文件 | 内容 |
| --- | --- |
| [dijkstra.md](graph/dijkstra.md) | 堆优化 Dijkstra、次短路、路径还原 |
| [dijkstra_layered.md](graph/dijkstra_layered.md) | 分层图最短路、最短路二分 |
| [spfa.md](graph/spfa.md) | 负权最短路 / 负环判定、Johnson 全源最短路 |
| [kruskal.md](graph/kruskal.md) | 最小生成树：Kruskal、Prim、堆优化 Prim |
| [toposort.md](graph/toposort.md) | 拓扑排序（Kahn）、拓扑序计数与 DP、反向图技巧 |
| [tarjan_scc.md](graph/tarjan_scc.md) | Tarjan 强连通分量、缩点 |
| [tarjan_bcc.md](graph/tarjan_bcc.md) | 边双连通分量（e-DCC）、点双连通分量（v-DCC）、割点与桥 |
| [hungarian.md](graph/hungarian.md) | 二分图判定、匈牙利算法、配对方案输出 |

### 数学（math）

| 文件 | 内容 |
| --- | --- |
| [sieve.md](math/sieve.md) | 埃氏筛、线性筛（同筛 φ / μ / 最小质因子） |
| [exgcd.md](math/exgcd.md) | 扩展欧几里得、取模防溢出 |
| [inverse.md](math/inverse.md) | 逆元：费马小定理、exgcd、线性递推批量求逆元 |
| [CRT.md](math/CRT.md) | 中国剩余定理（CRT）、扩展中国剩余定理（EXCRT） |
| [Euler.md](math/Euler.md) | 欧拉定理 / 扩展欧拉定理、幂塔降幂 |
| [comb.md](math/comb.md) | 组合数：阶乘逆元、Lucas、杨辉三角递推、分解质因子、错排数 |
| [exLucas.md](math/exLucas.md) | 扩展卢卡斯定理 |
| [catalan.md](math/catalan.md) | 卡特兰数：通项、递推与应用实例 |
| [in-exclusion.md](math/in-exclusion.md) | 容斥原理 |
| [probability.md](math/probability.md) | 概率正推、马尔科夫链 |
| [expectation.md](math/expectation.md) | 期望：正推 / 逆推、DAG 期望、期望线性性、无穷过程期望递推 |
| [expect_dag.md](math/expect_dag.md) | DAG 上的期望 DP |
| [misc.md](math/misc.md) | 数学公式与结论速查（组合、数论、数列求和、概率期望、博弈、几何、线代等） |
| [problem_list.md](math/problem_list.md) | 数学模板配套题单汇总 |

### 字符串（string）

| 文件 | 内容 |
| --- | --- |
| [kmp.md](string/kmp.md) | KMP 匹配、最小循环节应用 |
| [hash.md](string/hash.md) | 字符串哈希 |
| [trie.md](string/trie.md) | 字典树（含 Rust 版本） |

### 杂项（misc）

| 文件 | 内容 |
| --- | --- |
| [Fast_io.md](misc/Fast_io.md) | 快读快写：整数版、安全版、字符串版、完整版 |

## 使用说明

- 代码默认基于 **C++17**（使用 `<bits/stdc++.h>`，需 GCC 环境），提交时注意目标 OJ 的编译器与标准设置。
- 各模板标题后括号内为验证题目编号：`洛谷xxxx` 指洛谷主题库，`CFxxxx` 指 Codeforces，`LCxxxx` 指 LeetCode。
- 模板以实战可直接提交为目标，多数自带 `ios::sync_with_stdio(false)` 加速；卡常题目可配合 [Fast_io.md](misc/Fast_io.md) 使用。
- 数学部分建议先读 [misc.md](math/misc.md) 的公式速查与易错点，再查对应模板。

## 说明

模板来源于个人学习、刷题与赛后整理，部分写法参考了公开题解与社区资料。如发现错误或更优写法，欢迎提 Issue / PR。