---
title: SYUCT-ACM-暑假训练:CF2241(div3)
date: 2026-07-08 18:00:00
tags: [算法,ACM]
categories: 算法竞赛
description: CodeForce Round 1107 (Div. 3) (5/7)
---

# CodeForce Round 1107 (Div. 3) 题解

锐评：E >>> C > D > B > A

---

## A. Divide and Conquer

### 题目大意

给定两个正整数 x 和 y，每次操作可以选择 x 的一个因数 z，将 x 变为 x/z。问能否通过若干次操作将 x 变为 y。

### 解题思路

仔细分析操作的本质：每次操作都是将 x 除以它的一个因数。这意味着我们可以把 x 分解质因数后，逐步去掉一些质因子。

- 如果 y 是 x 的因数（即 x % y == 0），那么答案是 YES
- 否则，答案是 NO

因为如果 y 能整除 x，我们可以通过一系列除法操作将 x 变成 y；反之，如果 y 不能整除 x，任何除法操作都无法得到 y。

### 代码实现

```cpp
void solve()
{
    int x, y;
    cin >> x >> y;
    if (x % y == 0) cout << "YES" << endl;
    else cout << "NO" << endl;
}
```

---

## B. Good times Good times

### 题目大意

一个整数如果最多只包含两种不同的数字，则称为"好数"（good）。给定一个好数 x，找一个好数 y（2 ≤ y ≤ 10^9），使得 x × y 也是好数。

### 解题思路

观察题目要求：
- y 本身是好数
- x × y 也是好数

代码采用的构造方法：选择 y = 10^len + 1，其中 len 是 x 的位数。

例如：
- x = 8（len=1），y = 10^1 + 1 = 11 → 8 × 11 = 88（好数）
- x = 73（len=2），y = 10^2 + 1 = 101 → 73 × 101 = 7373（好数）
- x = 299（len=3），y = 10^3 + 1 = 1001 → 299 × 1001 = 299299（好数）

这样做的原因：
- y = 10^len + 1 只包含数字 1 和 0，显然是好数
- x × (10^len + 1) = x × 10^len + x，结果是 x 重复两次，通常也是好数

通俗易懂的说法就是在数前面加上原数，比如：
- 9 → 99
- 78 → 7878
- 114514 → 114514114514

### 代码实现

```cpp
void solve()
{
    string s;
    cin >> s;
    int len = s.size();
    long long ans = 1;
    for (int i = 1; i <= len; i++)
        ans *= 10;
    ans++;
    cout << ans << endl;
}
```

**解释**：`ans = 10^len + 1`，例如 len=1 时 ans=11，len=2 时 ans=101，len=3 时 ans=1001。这些数都只包含 1 和 0，是好数。

---

## C. RemovevomeR

### 题目大意

给定一个二进制字符串（只包含 0 和 1），每次操作可以选择一个长度至少为 2 的回文子串，并删除其中一个字符。求经过若干次操作后，字符串的最小可能长度。

### 解题思路

回文子串的性质：
- 任何连续相同的字符组成的子串都是回文（如 "000"、"11"）
- 单字符无法进行操作

**关键观察与证明：**

1. **全 0 或全 1 的情况**：整个字符串就是一个回文，可以不断删除直到只剩 1 个字符。

2. **字符串中有两种字符的情况**：
   - **情况一：只有一次变化（cnt = 1）**：
     - 字符串形如 `000...0111...1` 或 `111...1000...0`
     - 无法删除到只剩 1 个字符，因为最后会剩下一个 0 和一个 1，无法形成长度 ≥ 2 的回文子串
     - 例如 "110" → 删除一个 1 → "10"（无法继续删除）

   - **情况二：变化次数 ≥ 2（cnt ≥ 2）**：
     - 字符串形如 `0...01...10...0` 或 `1...10...01...1` 等
     - 可以通过以下策略删除到只剩 1 个字符：
       - 利用中间的回文区域逐步删除两侧的字符
       - 例如 "110011"：先删除中间的 00 中的一个，然后利用新形成的回文继续删除

### 代码实现

```cpp
void solve()
{
    int n;
    cin >> n;
    string str;
    cin >> str;
    int cnt = 0;
    for (int i = 0; i < n-1; i++)
    {
        if (str[i] != str[i+1]) cnt++;
    }
    if (cnt == 1) cout << 2 << endl;
    else cout << 1 << endl;
}
```

---

## D. An Alternative Way

### 题目大意

给定两个数组 a 和 b，长度都是 n。可以对数组 a 进行任意次如下操作：

1. 选择两个下标 l 和 r（1 ≤ l ≤ r ≤ n）
2. 对于 l 到 r 中的每个下标 i：
   - 如果 (i-l) 是偶数，a[i] += 1
   - 如果 (i-l) 是奇数，a[i] -= 1

问能否通过这些操作将数组 a 变成数组 b。

### 解题思路

**操作的数学性质：**

对于选定区间 [l, r]，操作的效果是：
- a[l] += 1（i-l = 0，偶数）
- a[l+1] -= 1（i-l = 1，奇数）
- a[l+2] += 1（i-l = 2，偶数）
- a[l+3] -= 1（i-l = 3，奇数）
- ...

可以看出，相邻位置的增量是相反的。

**关键观察：**

我们注意到：可以将数组分成2个2个的小段，对于每个小段：
1. 选择同一个数作为操作区间，可以令这个数一直增加
2. 选择两个数作为操作区间，那么发生的操作是对当前值减少，对左值增加。

**贪心策略：**

直接从后往前遍历，如果a[i] ≤ b[i]，将 a[i] 调整为 b[i]（可以通过单点加 1 操作实现）
如果 a[i] > b[i]，将多余的部分 (a[i] - b[i]) 传递给 a[i-1]

最后检查第一个位置是否满足 a[1] ≤ b[1] 即可

**正确性证明：**

- 从右向左处理确保了右边的位置不会再被修改
- 传递差值操作是可逆的（可以反向操作把差值传回来）
- 如果最终 a[1] ≤ b[1]，可以通过单点加 1 操作达到目标

### 代码实现

```cpp
void solve()
{
    int n;
    cin >> n;
    vector<long long> a(n+1), b(n+1);
    for (int i = 1; i <= n; i++) cin >> a[i];
    for (int i = 1; i <= n; i++) cin >> b[i];
    
    for (int i = n; i >= 2; i--)
    {
        if (a[i] <= b[i]) a[i] = b[i];
        else
        {
            a[i-1] += (a[i] - b[i]);
            a[i] = b[i];
        }
    }
    
    if (a[1] <= b[1]) cout << "YES" << endl;
    else cout << "NO" << endl;
}
```

**解释**：从右向左处理，将每个位置的多余值传递给左边位置。最后检查第一个位置是否满足条件。

---

## E. Fair and Square

### 题目大意

给定一棵 n 个节点的树，每个节点上有一个整数 a_i。对于任意两个不同的节点 u 和 v，定义 p(u, v) 为 u 到 v 路径上所有节点值的乘积。

一个三元组 {u, v, w} 被称为"好的"，当且仅当 p(u, v) × p(v, w) × p(w, u) 是一个完全平方数。

求好的三元组的数量。

### 解题思路

**数学分析：**

首先分析条件：p(u, v) × p(v, w) × p(w, u) 是完全平方数。

对于树中的任意三个节点 u, v, w，它们的路径必然交于一点 c（可能是其中一个节点）。

设路径上各节点的值的质因数分解为：
- p(u, v) 包含路径 u-v 上的所有节点值
- p(v, w) 包含路径 v-w 上的所有节点值  
- p(w, u) 包含路径 w-u 上的所有节点值

三者相乘后：
- c 处的值出现 3 次
- 其他节点值出现 0 或 2 次

对于乘积是完全平方数，每个质因子的指数必须是偶数。

**关键发现：**

对于三元组 {u, v, w}，设它们的公共交点为 c，则：
- 如果 c 处的值是完全平方数，则乘积是完全平方数（因为 3 次 × 平方数 = 平方数）
- 如果 c 处的值不是完全平方数，则乘积不可能是完全平方数

因此，问题转化为：对于每个值是完全平方数的节点 c，计算以 c 为交点的三元组数量。

**组合计算：**

对于节点 c，设它有 k 个子树（包括向上的部分），第 i 个子树大小为 s_i。

- 两两组合：从不同子树中选两个节点，数量为 Σ(s_i × s_j)（i < j）
- 三三组合：从不同子树中选三个节点，数量为 Σ(s_i × s_j × s_k)（i < j < k）


### 代码实现

```cpp
void solve()
{
    int n;
    cin >> n;
    vector<int> val(n+1);
    for (int i = 1; i <= n; i++) cin >> val[i];
    
    vector<vector<int>> graph(n+1);
    for (int i = 1; i < n; i++)
    {
        int u, v;
        cin >> u >> v;
        graph[u].push_back(v);
        graph[v].push_back(u);
    }
    
    long long ans = 0;
    vector<int> fa(n+1, 0);
    vector<int> dfs_order;
    dfs_order.reserve(n);
    
    queue<int> qe;
    qe.push(1);
    fa[1] = -1;
    while (!qe.empty())
    {
        int u = qe.front();
        qe.pop();
        dfs_order.push_back(u);
        for (auto &v : graph[u])
        {
            if (v == fa[u]) continue;
            fa[v] = u;
            qe.push(v);
        }
    }
    
    vector<int> subtree(n+1, 1);
    for (int i = n-1; i >= 1; i--)
    {
        int u = dfs_order[i];
        subtree[fa[u]] += subtree[u];
    }
    
    for (int u = 1; u <= n; u++)
    {
        if (!isPerfectSquare(val[u])) continue;
        
        long long prefix = 0;
        long long c_pair = 0;
        long long c3_num = 0;
        
        for (auto &v : graph[u])
        {
            long long x_num;
            if (v == fa[u])
                x_num = n - subtree[u];
            else
                x_num = subtree[v];
            
            c3_num += c_pair * x_num;
            c_pair += prefix * x_num;
            prefix += x_num;
        }
        
        ans += c_pair + c3_num;
    }
    
    cout << ans << endl;
}
```

### 算法分析

1. **B(D)FS 遍历**：建立树的父子关系，并获取遍历顺序
2. **子树大小计算**：从叶子向上计算每个节点的子树大小
3. **统计好的三元组**：
   - 对于每个值是完全平方数的节点 u
   - 计算以 u 为交点的三元组数量
   - 使用组合数学公式：两两组合 + 三三组合 //其实我不会组合数学，现搜的

**时间复杂度**：O(n)，适合 n ≤ 2×10^5 的规模。

---

## 总结

| 题目 | 知识点 | 难度评价 |
|------|--------|----------|
| A | 整除判断 | 入门 |
| B | 构造法 | 思维题 |
| C | 字符串分析 | 观察能力 |
| D | 贪心策略 | 不如B |
| E | 树结构 + 组合数学 + 贪心 | 中等偏难 |

---

## C++火车头

```cpp
#include <bits/stdc++.h>
using namespace std;

#define IOS ios::sync_with_stdio(false); cin.tie(nullptr); cout.tie(nullptr)
#define endl '\n'
#define int long long
#define ld long double
#define pb push_back
#define PII pair<int,int>
#define ull unsigned long long
#define i128 __int128
const int INF = 1e9+10;
const int LINF = 1e18+10;
const ld PI = acos(-1.0);
const ld EPS = 1e-9;
using ll = long long ;

void Asanagi()
{
    
}
signed main()
{
    IOS;
    int t = 1;
    cin >> t;
    while (t--)
    {
        Asanagi();
    }
    return 0;
}

```
## 完全数
```cpp
bool isPerfectSquare(long long x) {
    long long r = sqrt((long double)x);
    while (1LL * r * r < x) r++;
    while (1LL * r * r > x) r--;
    return 1LL * r * r == x;
}
```