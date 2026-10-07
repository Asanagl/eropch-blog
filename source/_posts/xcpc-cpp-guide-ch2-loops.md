---
title: XCPCer 从零开始的 C/C++ 入门指南——第 2 章 运算、判断与循环
date: 2026-10-07 18:45:00
slug: xcpc-cpp-guide-ch2-loops
tags: [XCPC, C语言, C++, 训练规划, 控制流]
categories: 算法竞赛
description: 系列第 2 章：从表达式、运算符和 bool 条件出发，系统学习 if、switch、for、while、do-while、break、continue、嵌套循环、枚举与复杂度直觉，并补充 ASCII 字符运算和常见判题错误。
---

# 第 2 章 运算、判断与循环

> 本文是《XCPCer 从零开始的 C/C++ 入门指南（算法竞赛向）》系列第 2 章，系列训练大纲见[本文](/2026/09/20/xcpc-cpp-guide-outline/)。

## 2.0 这一章为什么突然变长了

按照最初的大纲，这一阶段本来应该先单独讲 ASCII，再讲运算符，最后才讲循环。但本届新生的推进速度比预想中快，不少同学也已经自己学到了循环。既然如此，我们就不再把一个完整知识链拆成三次训练：本章把**表达式、条件判断和循环**一次接起来，顺手补齐第一章点到为止、但写循环题一定会用到的内容。

学完本章，你应该能独立完成下面这些事情：

- 把一句数学条件准确地翻译成 C++ 表达式；
- 使用 `if`、`else if`、`switch` 处理分支；
- 使用 `for`、`while`、`do-while` 重复执行代码；
- 用嵌套循环枚举数对、打印图形；
- 看到数据范围时，对程序会不会超时有一个初步判断；
- 遇到 CE、RE、WA、TLE 时，知道如何改动代码。

如果只安排一次训练，不必强求所有内容当场吃透。建议按下面的优先级学习：

- **本次训练必须掌握**：2.1 中的算术、比较与逻辑运算，2.2 字符运算，2.3 的 `if / else`，2.4 的 `for / while`，2.5 的 `break / continue`，以及 2.6 的基础嵌套循环；
- **下次训练必须掌握**：`switch`、条件运算符、`do-while`、枚举优化、复杂度细节和最后的二分模拟。

推进快的同学可以一路读完；还在消化基础的同学先守住“条件、边界、更新”这条主线，下一次训练再补加餐。


> **先算出一个条件，再根据条件决定执行什么；需要做很多次，就把这个决定放进循环。**


---

## 2.1 表达式：程序怎样“算出一个值”

把变量、常量和运算符连接起来，就得到一个**表达式**。表达式一定会产生一个值，例如：

- `a + b` 的值是两数之和；
- `a > b` 的值是 `true` 或 `false`；
- `x = 5` 在完成赋值的同时，整个表达式的值也是 `5`。

分号 `;` 通常不属于表达式，它表示一条语句结束。比如 `sum = a + b;` 中，`sum = a + b` 是表达式，末尾分号把它变成了一条表达式语句。

### 2.1.1 算术运算符

| 运算符 | 含义 | 示例 | 结果 |
| --- | --- | --- | --- |
| `+` | 加法 | `7 + 3` | `10` |
| `-` | 减法 / 取负 | `7 - 3` / `-7` | `4` / `-7` |
| `*` | 乘法 | `7 * 3` | `21` |
| `/` | 除法 | `7 / 3` | 整数运算时为 `2` |
| `%` | 取余 | `7 % 3` | `1` |

整数除法会直接舍去小数部分，结果向 0 截断。因此 `7 / 3` 是 `2`，`-7 / 3` 是 `-2`。如果需要 `2.333...`，至少让一个操作数变成浮点数，例如 `7.0 / 3` 或 `(double)7 / 3`。

`%` 只能用于整数。在 C++ 中，余数与被除数同号或为 0：`7 % 3` 是 `1`，`-7 % 3` 是 `-1`。所以当题目要求数学意义上位于 `[0, m-1]` 的余数，而 `x` 可能为负时，可以写成：

```cpp
#include <iostream>
using namespace std;

int main()
{
    int x = -7;
    int m = 3;
    int remainder = x % m;
    if (remainder < 0)
    {
        remainder += m;
    }

    cout << remainder << '\n';
    return 0;
}
```

运行结果：

```text
2
```

这里先算 `-7 % 3 = -1`，发现结果为负后再加上 `3`，得到数学中常用的非负余数 `2`。本技巧要求 `m > 0`。不要机械地写成 `(x % m + m) % m`：当 `x % m` 和 `m` 都很大时，中间的加法也可能让有符号整数溢出。

### 2.1.2 赋值与复合赋值

赋值运算符 `=` 的作用是把右边的值放进左边的变量：

```cpp
#include <iostream>
using namespace std;

int main()
{
    int score = 60;
    score = score + 10;
    cout << score << '\n';
    return 0;
}
```

运行结果：

```text
70
```

当一个变量要在自身基础上修改时，可以使用复合赋值：

| 完整写法 | 简写 |
| --- | --- |
| `x = x + y` | `x += y` |
| `x = x - y` | `x -= y` |
| `x = x * y` | `x *= y` |
| `x = x / y` | `x /= y` |
| `x = x % y` | `x %= y` |

`sum += i;` 是循环求和中最高频的一行，意思是“把本轮的 `i` 加进累计答案 `sum`”。

### 2.1.3 自增与自减

`++` 让变量增加 1，`--` 让变量减少 1：

```cpp
#include <iostream>
using namespace std;

int main()
{
    int a = 5;
    int b = a++; // 先把旧值 5 交给 b，再让 a 变成 6

    int c = 5;
    int d = ++c; // 先让 c 变成 6，再把 6 交给 d

    cout << a << ' ' << b << '\n';
    cout << c << ' ' << d << '\n';
    return 0;
}
```

运行结果：

```text
6 5
6 6
```

单独占一行时，`i++` 与 `++i` 对本章的基本整数循环没有区别，都让 `i` 增加 1。初学阶段不要写 `a = a++`、`cout << i++ + ++i` 之类炫技表达式：有些写法会产生未定义行为，即使能运行也非常容易看错。

### 2.1.4 关系运算符：比较之后得到 bool

| 运算符 | 含义 |
| --- | --- |
| `==` | 等于 |
| `!=` | 不等于 |
| `<` | 小于 |
| `>` | 大于 |
| `<=` | 小于等于 |
| `>=` | 大于等于 |

关系表达式的结果是 `bool`：成立为 `true`，不成立为 `false`。

```cpp
#include <iostream>
using namespace std;

int main()
{
    int a = 3;
    int b = 5;

    cout << (a < b) << '\n';
    cout << (a == b) << '\n';
    return 0;
}
```

运行结果：

```text
1
0
```

`cout` 默认把 `true` 输出为 `1`，把 `false` 输出为 `0`。最容易写错的是：**判断相等用两个等号 `==`，赋值才用一个等号 `=`。**

### 2.1.5 逻辑运算符：并且、或者、不是

关系运算符只能描述一个条件，例如 `age >= 18`。当题目同时提出多个条件时，就要用逻辑运算符把它们连接起来：

| 运算符 | 中文含义 | 何时为 `true` | 示例 |
| --- | --- | --- | --- |
| `&&` | 并且 | 左右条件同时成立 | `x >= 1 && x <= 100` |
| <code>&#124;&#124;</code> | 或者 | 左右条件至少一个成立 | `x < 1 || x > 100` |
| `!` | 取反 / 不是 | 原条件不成立 | `!(x == 0)` |

设 `A`、`B` 都是条件，三种逻辑运算的结果如下：

| `A` | `B` | `A` 并且 `B` | `A` 或者 `B` | 非 `A` |
| --- | --- | --- | --- | --- |
| `false` | `false` | `false` | `false` | `true` |
| `false` | `true` | `false` | `true` | `true` |
| `true` | `false` | `false` | `true` | `false` |
| `true` | `true` | `true` | `true` | `false` |

记忆方法很简单：`&&` 比较严格，要求两边都对；`||` 比较宽松，只要一边对；`!` 直接把真假翻转。

例如，判断 `x` 是否位于闭区间 `[1, 100]`：

```cpp
#include <iostream>
using namespace std;

int main()
{
    int x;
    cin >> x;

    bool inRange = (x >= 1 && x <= 100);
    bool outOfRange = (x < 1 || x > 100);
    bool notInRange = !inRange;

    cout << inRange << ' '
         << outOfRange << ' '
         << notInRange << '\n';
    return 0;
}
```

输入：

```text
73
```

输出：

```text
1 0 0
```

这里 `inRange` 表示“范围内”，`outOfRange` 和 `notInRange` 都表示“范围外”，所以后二者结果相同。由此也能看到：

```text
!(x >= 1 && x <= 100)
```

等价于：

```text
x < 1 || x > 100
```

数学里常写 `1 <= x <= 100`，但 C++ **不能照抄**。C++ 会先计算 `1 <= x`，得到 `0` 或 `1`，再拿这个结果与 `100` 比较；由于 `0` 和 `1` 都小于等于 `100`，所以对这里的整数 `x`，这个错误表达式永远为真。即使输入 `1000` 也拦不住。必须拆成 `x >= 1 && x <= 100`。

另外，逻辑运算符是 `&&`、`||`、`!`，不要漏写成单个 `&` 或 `|`。后两者是位运算符，含义不同，会在后续章节单独讲。

### 2.1.6 短路求值：后半句可能根本不算

逻辑运算符有一条非常重要的规则：

- `A && B` 中，如果 `A` 已经为假，整体必假，`B` 不再计算；
- `A || B` 中，如果 `A` 已经为真，整体必真，`B` 不再计算。

这叫**短路求值**。它不只是优化，还能保护危险运算。判断 `a` 能否整除 `b` 时，必须先排除除数为 0：

```cpp
#include <iostream>
using namespace std;

int main()
{
    int a, b;
    cin >> a >> b;

    if (a != 0 && b % a == 0)
    {
        cout << "divisible\n";
    }
    else
    {
        cout << "not divisible\n";
    }
    return 0;
}
```

输入：

```text
0 12
```

输出：

```text
not divisible
```

因为 `a != 0` 为假，右边的 `b % a` 不会执行，避免了对 0 取模。顺序如果反过来写成 `b % a == 0 && a != 0`，保护就失效了。

### 2.1.7 运算符优先级：拿不准就加括号

本章常用运算符的优先级从高到低可先记为：

1. 括号 `()`；
2. 单目运算 `!`、`++`、`--`、取负；
3. `*`、`/`、`%`；
4. `+`、`-`；
5. `<`、`<=`、`>`、`>=`；
6. `==`、`!=`；
7. `&&`；
8. `||`；
9. 赋值 `=`、`+=` 等。

不需要死背整张 C++ 优先级表。比赛中一旦可能误读，直接加括号。`(year % 4 == 0 && year % 100 != 0) || year % 400 == 0` 就比依赖记忆安全得多。

### 2.1.8 整数运算仍然要防溢出

第一章讲过：右侧表达式先完成运算，再把结果赋给左侧变量。因此即使答案变量是 `long long`，`int * int` 仍然可能先溢出。

```cpp
#include <iostream>
using namespace std;

int main()
{
    int a = 100000;
    int b = 100000;
    long long product = 1LL * a * b;

    cout << product << '\n';
    return 0;
}
```

运行结果：

```text
10000000000
```

这里的 `1LL` 让后续乘法从一开始就在 `long long` 中完成。补充一条技术上必须准确的说明：**C++ 对有符号整数溢出没有规定结果**，不能依赖它“必然绕回”；无符号整数才按模意义回绕。竞赛中最实际的处理方式仍然是提前选够大的类型。

### 2.1.9 浮点数不要直接判断相等

许多小数不能被二进制浮点数精确表示，因此计算结果可能只是在目标值附近。与其写 `a == b`，通常比较两者差的绝对值是否足够小：

```cpp
#include <cmath>
#include <iostream>
using namespace std;

int main()
{
    double a = 0.1 + 0.2;
    double b = 0.3;
    const double EPS = 1e-9;

    if (fabs(a - b) < EPS)
    {
        cout << "equal\n";
    }
    else
    {
        cout << "not equal\n";
    }
    return 0;
}
```

运行结果：

```text
equal
```

`EPS` 取多大取决于题目允许的误差与数值规模，`1e-9` 只是常见入门值，不是万能常量。

---

## 2.2 加餐：ASCII 与字符运算

第一章已经说过，`char` 本质上存的是一个整数编码。竞赛中常见英文字母、数字和符号使用 ASCII 编码，其中最值得记住的是三个**连续区间**：

| 字符范围 | 十进制编码 | 关键性质 |
| --- | --- | --- |
| `'0'`～`'9'` | 48～57 | 连续，相减可得到数值 |
| `'A'`～`'Z'` | 65～90 | 连续 |
| `'a'`～`'z'` | 97～122 | 连续 |

因此：

- 数字字符转数值：`digit = c - '0'`；
- 小写字母转大写：`c - 'a' + 'A'`；
- 大小写字母的 ASCII 差值为 `'a' - 'A'`。

`char` 参与算术运算前会先提升为 `int`。因此 `'A' + 1` 的值虽然是编码 `66`，但它的类型是 `int`；直接 `cout << 'A' + 1` 会输出 `66`，不会输出 `B`。若要把结果当作字符输出，需要先转换回 `char`：

```cpp
#include <iostream>
using namespace std;

int main()
{
    cout << ('A' + 1) << '\n';
    cout << static_cast<char>('A' + 1) << '\n';
    return 0;
}
```

运行结果：

```text
66
B
```

```cpp
#include <iostream>
using namespace std;

int main()
{
    char digitCharacter = '7';
    int digitValue = digitCharacter - '0';

    char lower = 'c';
    char upper = lower - 'a' + 'A';

    cout << digitValue + 5 << '\n';
    cout << upper << '\n';
    return 0;
}
```

运行结果：

```text
12
C
```

注意 `'7'` 是字符，数值是其编码；`7` 才是整数。`'7' - '0'` 得到整数 `7`，而不是字符 `'7'`。此外，不要默认所有字符编码都适合这种手算；本章只讨论竞赛中常见的 ASCII 数字与英文字母。

结合条件判断，我们可以自行识别字符类型：

```cpp
#include <iostream>
using namespace std;

int main()
{
    char c;
    cin >> c;

    if (c >= '0' && c <= '9')
    {
        cout << "digit " << c - '0' << '\n';
    }
    else if (c >= 'A' && c <= 'Z')
    {
        cout << "uppercase\n";
    }
    else if (c >= 'a' && c <= 'z')
    {
        cout << "lowercase\n";
    }
    else
    {
        cout << "other\n";
    }
    return 0;
}
```

输入：

```text
8
```

输出：

```text
digit 8
```

---

## 2.3 条件判断：让程序选择道路

程序默认从上到下执行。条件语句让它在岔路口根据数据选择不同的道路。

### 2.3.1 单分支 if

基本形式为 `if (条件) { ... }`。条件为真时执行花括号里的语句，否则直接跳过。

```cpp
#include <iostream>
using namespace std;

int main()
{
    int score;
    cin >> score;

    if (score >= 60)
    {
        cout << "pass\n";
    }
    return 0;
}
```

输入 `75` 时输出 `pass`；输入 `40` 时没有输出。

C++ 的条件位置不只接受 `bool`：数值 `0` 会被视为 `false`，非零值会被视为 `true`。因此 `if (x)` 等价于判断 `x != 0`，`if (!x)` 等价于判断 `x == 0`。初学阶段写完整比较通常更易读。

### 2.3.2 双分支 if / else

如果两种情况必然二选一，使用 `else`：

```cpp
#include <iostream>
using namespace std;

int main()
{
    int n;
    cin >> n;

    if (n % 2 == 0)
    {
        cout << "even\n";
    }
    else
    {
        cout << "odd\n";
    }
    return 0;
}
```

输入：

```text
13
```

输出：

```text
odd
```

### 2.3.3 多分支 else if

多个区间分类时，从上到下依次检查；命中第一个成立条件后，后面的分支不再执行。

```cpp
#include <iostream>
using namespace std;

int main()
{
    int score;
    cin >> score;

    if (score < 0 || score > 100)
    {
        cout << "invalid\n";
    }
    else if (score >= 90)
    {
        cout << "A\n";
    }
    else if (score >= 80)
    {
        cout << "B\n";
    }
    else if (score >= 60)
    {
        cout << "C\n";
    }
    else
    {
        cout << "D\n";
    }
    return 0;
}
```

输入：

```text
86
```

输出：

```text
B
```

这里条件必须从严格到宽松排列。若先写 `score >= 60`，那么 95 也会在这一项就被拦住，永远走不到 A。

### 2.3.4 嵌套 if 与悬空 else

`if` 内部还能再放 `if`。但省略花括号时，`else` 会与**最近且尚未配对的 `if`** 结合，这就是“悬空 else”问题。统一写花括号能直接消灭这种歧义。

```cpp
#include <iostream>
using namespace std;

int main()
{
    int age;
    bool hasTicket;
    cin >> age >> hasTicket;

    if (age >= 18)
    {
        if (hasTicket)
        {
            cout << "enter\n";
        }
        else
        {
            cout << "buy a ticket\n";
        }
    }
    else
    {
        cout << "too young\n";
    }
    return 0;
}
```

本讲义后续即使分支只有一行，也会尽量保留花括号。少打两次回车，不值得换一个凌晨两点的 bug。

### 2.3.5 switch：按离散值分支 ~~// 其实零个人用这鬼东西~~

当分支条件都是“某个变量等于一个确定的整数或字符”时，`switch` 往往比很长的 `else if` 更整齐。

```cpp
#include <iostream>
using namespace std;

int main()
{
    char operation;
    int a, b;
    cin >> a >> operation >> b;

    switch (operation)
    {
        case '+':
            cout << a + b << '\n';
            break;
        case '-':
            cout << a - b << '\n';
            break;
        case '*':
            cout << a * b << '\n';
            break;
        case '/':
            if (b == 0)
            {
                cout << "division by zero\n";
            }
            else
            {
                cout << a / b << '\n';
            }
            break;
        default:
            cout << "unknown operation\n";
            break;
    }
    return 0;
}
```

输入：

```text
20 * 6
```

输出：

```text
120
```

`switch` 的注意事项：

1. `case` 后面必须是编译期可确定的整型、字符或枚举常量，不能写范围，也不能直接比较字符串；
2. 通常每个 `case` 末尾写 `break`，否则会继续执行下一个分支，这叫“贯穿”；
3. `default` 类似最后的 `else`，处理所有未匹配情况；
4. 区间判断（例如分数等级）仍然更适合 `if / else if`。

### 2.3.6 条件运算符 `?:`

简单的二选一可以写成：

```cpp
#include <iostream>
using namespace std;

int main()
{
    int a, b;
    cin >> a >> b;

    int larger = (a > b) ? a : b;
    cout << larger << '\n';
    return 0;
}
```

输入 `8 5`，输出 `8`。其含义是：条件 `a > b` 成立时取 `a`，否则取 `b`。三目运算符适合短小表达式，不要把许多层 `?:` 套在一起，那会让代码像谜语。

### 2.3.7 综合例题：判断闰年

公历闰年的规则是：能被 400 整除，或者能被 4 整除但不能被 100 整除。

```cpp
#include <iostream>
using namespace std;

int main()
{
    int year;
    cin >> year;

    bool leap = (year % 400 == 0)
             || (year % 4 == 0 && year % 100 != 0);

    if (leap)
    {
        cout << "leap year\n";
    }
    else
    {
        cout << "common year\n";
    }
    return 0;
}
```

输入：

```text
2000
```

输出：

```text
leap year
```

这道题是翻译条件的好例子。先把中文中的“或者”“并且”“不能”分别对应到 `||`、`&&`、`!=`，再用括号把逻辑层次写出来。

---

## 2.4 循环：让同一段代码工作很多次

循环由三件事组成：

1. **初始状态**：从哪里开始；
2. **继续条件**：什么时候还要做；
3. **状态更新**：每次做完后怎样接近结束。

少了第三项，循环条件可能永远为真，程序就会卡在死循环里。

### 2.4.1 while：次数未知，条件明确

`while` 会先检查条件，条件为真才执行循环体。所以它可能一次也不执行。

下面把一个正整数的十进制数位倒序输出：

```cpp
#include <iostream>
using namespace std;

int main()
{
    long long n;
    cin >> n;

    if (n == 0)
    {
        cout << 0;
    }
    while (n > 0)
    {
        cout << n % 10;
        n /= 10;
    }
    cout << '\n';
    return 0;
}
```

输入：

```text
12030
```

输出：

```text
03021
```

每轮用 `n % 10` 取末位，再用 `n /= 10` 删除末位。循环次数取决于数字有多少位，事先不必知道。

### 2.4.2 do-while：至少执行一次

`do-while` 先执行循环体，再检查条件，因此无论条件如何，循环体至少执行一次。末尾的分号不能漏。

```cpp
#include <iostream>
using namespace std;

int main()
{
    int n = 0;
    do
    {
        cout << "Please enter a positive integer: ";
        if (!(cin >> n))
        {
            return 0;
        }
    }
    while (n <= 0);

    cout << "accepted " << n << '\n';
    return 0;
}
```

一次可能的运行过程：

```text
Please enter a positive integer: -2
Please enter a positive integer: 0
Please enter a positive integer: 7
accepted 7
```

在线评测题一般不会要求反复提示用户，所以 `do-while` 在竞赛代码中不如 `for`、`while` 常见，但“菜单至少显示一次”“输入至少处理一次”时很自然。示例中的 `if (!(cin >> n))` 用来处理 EOF 或非数字输入：一旦读取失败就直接结束，避免输入流一直处于失败状态而形成死循环。正式 OJ 通常会保证输入格式合法，但知道失败也能成为条件很有用。

### 2.4.3 for：次数或范围明确

`for` 把“初始化、继续条件、更新”放在一行：

```text
for (初始化; 继续条件; 更新)
{
    循环体
}
```

例如计算 `1 + 2 + ... + n`：

```cpp
#include <iostream>
using namespace std;

int main()
{
    int n;
    cin >> n;

    long long sum = 0;
    for (int i = 1; i <= n; i++)
    {
        sum += i;
    }

    cout << sum << '\n';
    return 0;
}
```

输入：

```text
100
```

输出：

```text
5050
```

这段循环的状态变化如下：

```text
i = 1 -> 条件成立 -> sum 加 1 -> i 变 2
i = 2 -> 条件成立 -> sum 加 2 -> i 变 3
...
i = n -> 条件成立 -> sum 加 n -> i 变 n+1
i = n+1 -> 条件不成立 -> 循环结束
```

### 2.4.4 for 与 while 可以互相改写

上面的求和循环也可以写为：

```cpp
#include <iostream>
using namespace std;

int main()
{
    int n;
    cin >> n;

    long long sum = 0;
    int i = 1;
    while (i <= n)
    {
        sum += i;
        i++;
    }

    cout << sum << '\n';
    return 0;
}
```

两者能力没有本质区别。经验上：

- “从 1 到 n”“重复 t 次”优先用 `for`；
- “直到某条件不成立”“不断拆数位”优先用 `while`；
- 必须先做一次再判断时用 `do-while`。

选择能让意图最明显的写法。

### 2.4.5 循环边界：`< n` 还是 `<= n`

边界错误是初学者最常见的 WA。写循环前先用中文说清楚要遍历的集合：

- 遍历 `1, 2, ..., n`：`for (int i = 1; i <= n; i++)`；
- 执行恰好 `n` 次，以 0 开始计数：`for (int i = 0; i < n; i++)`；
- 遍历 `l, l+1, ..., r`：`for (int i = l; i <= r; i++)`；
- 倒序遍历 `n, n-1, ..., 1`：`for (int i = n; i >= 1; i--)`。

不要凭手感改 `<` 和 `<=`。拿 `n = 1` 在纸上走一遍，往往立即能看出到底少一轮还是多一轮。

### 2.4.6 倒序循环与 unsigned 陷阱

倒序循环的控制变量优先使用 `int` 或 `long long`：

```cpp
#include <iostream>
using namespace std;

int main()
{
    int n;
    cin >> n;

    for (int i = n; i >= 0; i--)
    {
        cout << i << (i == 0 ? '\n' : ' ');
    }
    return 0;
}
```

输入 `5`，输出：

```text
5 4 3 2 1 0
```

如果把 `i` 写成 `unsigned int`，条件 `i >= 0` 永远成立。`i` 在 0 之后不会变成 -1，而会回绕到一个巨大的正数，循环无法正常结束。这正是第一章建议初学阶段慎用无符号类型的原因之一。

### 2.4.7 死循环不一定是错，循环代码必须包含剪枝（跳出）

`while (true)` 和 `for (;;)` 都表示条件永远为真。若循环内存在明确的 `break` 出口，它可以是合法写法；若没有出口，通常会得到 TLE/RE。

不过，如果题目要求“有多少组就读多少组，直到文件结束（EOF）”，不需要写死循环再手动判断输入是否结束，直接使用 `while (cin >> n)`：

```cpp
#include <iostream>
using namespace std;

int main()
{
    int sum = 0;
    int n;

    while (cin >> n)
    {
        sum += n;
    }

    cout << sum << '\n';
    return 0;
}
```

输入：

```text
3 5 -2
```

输出：

```text
6
```

只要成功读到一个整数，`cin >> n` 就可以视为真，循环处理这一组数据；读到 EOF 后，表达式变为假，循环自然结束。如果每组包含多个字段，可以写成 `while (cin >> n >> m)`，确保一整组的组头读完后再进入循环。若题目给了明确的特殊结束标记，或者退出条件更复杂，再使用普通 `while` 或 `while (true)` 配合 `break`。

### 2.4.8 多组测试：每一组都要重新开始

本讲义把多组输入统一成两种写法：

1. 题目给出测试组数 `t`：先写 `int t; cin >> t;`，再用 `while (t--)`；
2. 题目不提供组数，要求读到 EOF：使用上一节的 `while (cin >> n)`。

> **训练要求：** 多组输入请直接使用上面两种模板。请避免写成 `if (!(cin >> t))` 或 `if (!(cin >> n))` 这种形式，不然我会把你算作 AI 代码处理。

不要在同一道题里混用两套模板。下面演示第一种：每完成一组，`t` 减少 1，直到变成 0。

下面对每个非负整数 `n` 分别计算 `1 + 2 + ... + n`：

```cpp
#include <iostream>
using namespace std;

int main()
{
    int t;
    cin >> t;

    while (t--)
    {
        int n;
        cin >> n;

        if (n < 0 || n > 1000000)
        {
            cout << "invalid\n";
            continue;
        }

        long long sum = 0;
        for (int i = 1; i <= n; i++)
        {
            sum += i;
        }
        cout << sum << '\n';
    }
    return 0;
}
```

输入：

```text
3
3
0
5
```

输出：

```text
6
0
15
```

`sum` 必须定义在 `while (t--)` 内部，或在每组开始时重新赋为 0。若把它放在外面又忘记清零，后一组就会带着前一组的答案继续累加。竞赛题会保证 `t` 是非负数，并保证输入数量与格式符合题面，因此这里直接使用 `cin >> t` 和 `cin >> n`。循环结束后 `t` 已经被减到 `-1`，不要再拿它表示原来的测试组数。示例限制 `n <= 10^6`，既保证循环能及时结束，也保证答案可由 `long long` 安全保存。

---

## 2.5 break 与 continue：改变本轮流程

### 2.5.1 break：立刻结束当前循环

下面判断 `n` 是否为质数。只要找到一个因数，就已经能确定“不是质数”，没有必要继续检查。

```cpp
#include <iostream>
using namespace std;

int main()
{
    int n;
    cin >> n;

    bool isPrime = (n >= 2);
    for (int divisor = 2; 1LL * divisor * divisor <= n; divisor++)
    {
        if (n % divisor == 0)
        {
            isPrime = false;
            break;
        }
    }

    if (isPrime)
    {
        cout << "prime\n";
    }
    else
    {
        cout << "not prime\n";
    }
    return 0;
}
```

输入：

```text
97
```

输出：

```text
prime
```

为什么只检查到 `divisor * divisor <= n`？如果 `n = a * b` 且 `a`、`b` 都大于 `sqrt(n)`，乘积就会大于 `n`，矛盾。因此合数一定存在一个不超过平方根的因数。

条件中写 `1LL * divisor * divisor` 是为了避免两个 `int` 相乘先溢出。

### 2.5.2 continue：跳过本轮剩余部分

下面计算 `1` 到 `n` 中所有奇数的和：

```cpp
#include <iostream>
using namespace std;

int main()
{
    int n;
    cin >> n;

    long long sum = 0;
    for (int i = 1; i <= n; i++)
    {
        if (i % 2 == 0)
        {
            continue;
        }
        sum += i;
    }

    cout << sum << '\n';
    return 0;
}
```

输入 `7`，输出：

```text
16
```

遇到偶数时，`continue` 跳过本轮的 `sum += i`，然后进入下一轮。注意：

- `for` 执行 `continue` 后，仍会先执行更新表达式 `i++`，再判断条件；
- `while` 执行 `continue` 后会直接回到条件判断。如果更新语句写在循环体末尾，它可能被一起跳过，从而造成死循环。

下面这种 `while` 写法就很危险：当 `i` 为偶数时，`i++` 永远执行不到。更稳妥的方法是把更新放在 `continue` 之前，或重新组织条件，让每条路径都能更新状态。

### 2.5.3 break 只结束最近一层循环

在嵌套循环里，`break` 只会退出它直接所在的那一层。想同时退出两层，可以使用一个 `bool` 标记：

```cpp
#include <iostream>
using namespace std;

int main()
{
    int target;
    cin >> target;

    bool found = false;
    int answerA = 0;
    int answerB = 0;

    for (int a = 0; a <= 9 && !found; a++)
    {
        for (int b = 0; b <= 9; b++)
        {
            if (10 * a + b == target)
            {
                answerA = a;
                answerB = b;
                found = true;
                break;
            }
        }
    }

    if (found)
    {
        cout << answerA << ' ' << answerB << '\n';
    }
    else
    {
        cout << "not found\n";
    }
    return 0;
}
```

输入 `42`，输出：

```text
4 2
```

外层循环条件中的 `&& !found` 让它在找到答案后也结束。

---

## 2.6 嵌套循环：循环里面再放循环

### 2.6.1 先看执行顺序

嵌套循环的执行方式是：外层每进行一轮，内层都完整执行一遍。

```cpp
#include <iostream>
using namespace std;

int main()
{
    for (int row = 1; row <= 2; row++)
    {
        for (int column = 1; column <= 3; column++)
        {
            cout << '(' << row << ',' << column << ") ";
        }
        cout << '\n';
    }
    return 0;
}
```

运行结果：

```text
(1,1) (1,2) (1,3)
(2,1) (2,2) (2,3)
```

外层 2 轮，每轮内层 3 轮，总共执行 `2 * 3 = 6` 次输出。

### 2.6.2 打印直角三角形

遇到带图形的题不要盯着整幅图发呆。把问题拆开：一共有多少行？第 `i` 行输出多少个字符？图里的数字有什么规律？

```cpp
#include <iostream>
using namespace std;

int main()
{
    int n;
    cin >> n;

    for (int row = 1; row <= n; row++)
    {
        for (int count = 1; count <= row; count++)
        {
            cout << '*';
        }
        cout << '\n';
    }
    return 0;
}
```

输入：

```text
4
```

输出：

```text
*
**
***
****
```

外层控制行号 `row`，内层输出恰好 `row` 个星号，内层结束后再换行。

### 2.6.3 九九乘法表

```cpp
#include <iostream>
using namespace std;

int main()
{
    for (int i = 1; i <= 9; i++)
    {
        for (int j = 1; j <= i; j++)
        {
            cout << j << '*' << i << '=' << i * j << '\t';
        }
        cout << '\n';
    }
    return 0;
}
```

运行结果的前四行：

```text
1*1=1
1*2=2	2*2=4
1*3=3	2*3=6	3*3=9
1*4=4	2*4=8	3*4=12	4*4=16
```

上面结果中的 `\t` 表示制表符造成的间隔；不同终端的对齐宽度可能略有区别。

---

## 2.7 枚举：最朴素也最可靠的武器

**枚举**不是某个新语法，而是一种解题思路，一种算法：把可能的候选按某种顺序逐个尝试，检查哪些满足条件。

当候选数量不大时，枚举往往是最稳的正解，而不是“不会算法才暴力”。关键是回答三个问题：

1. 枚举什么变量？
2. 每个变量的范围是什么？
3. 怎样检查一个候选是否合法？

### 2.7.1 单变量枚举：找约数

```cpp
#include <iostream>
using namespace std;

int main()
{
    int n;
    cin >> n;

    for (int divisor = 1; divisor <= n; divisor++)
    {
        if (n % divisor == 0)
        {
            cout << divisor << ' ';
        }
    }
    cout << '\n';
    return 0;
}
```

输入：

```text
12
```

输出：

```text
1 2 3 4 6 12
```

枚举候选约数 `1` 到 `n`，用 `n % divisor == 0` 检查整除。

### 2.7.2 双变量枚举：百钱买百鸡的简化版

有 100 元钱，公鸡 5 元一只，母鸡 3 元一只，小鸡 1 元三只。恰好买 100 只且花完 100 元，求所有方案。

```cpp
#include <iostream>
using namespace std;

int main()
{
    for (int rooster = 0; rooster <= 20; rooster++)
    {
        for (int hen = 0; hen <= 33; hen++)
        {
            int chick = 100 - rooster - hen;
            if (chick >= 0
                && chick % 3 == 0
                && 5 * rooster + 3 * hen + chick / 3 == 100)
            {
                cout << rooster << ' '
                     << hen << ' '
                     << chick << '\n';
            }
        }
    }
    return 0;
}
```

运行结果：

```text
0 25 75
4 18 78
8 11 81
12 4 84
```

本来有三种鸡，但总数固定为 100，所以枚举公鸡和母鸡后，小鸡数可以直接算出，没有必要再套第三层循环。这叫**减少枚举维度**，是最早能接触到的优化思想。

### 2.7.3 枚举时避免重复

若要枚举满足 `1 <= a < b <= n` 的数对，内层不必从 1 开始：

```cpp
#include <iostream>
using namespace std;

int main()
{
    int n;
    cin >> n;

    for (int a = 1; a <= n; a++)
    {
        for (int b = a + 1; b <= n; b++)
        {
            cout << a << ' ' << b << '\n';
        }
    }
    return 0;
}
```

输入 `4`，输出：

```text
1 2
1 3
1 4
2 3
2 4
3 4
```

这样既不会出现 `(a, a)`，也不会把 `(1, 2)` 和 `(2, 1)` 重复计算。

### 2.7.4 边读边处理：不存数组也能统计

如果数据只需要使用一次，就可以每读入一个数立刻更新答案，不必等学完数组。下面同时计算一批数的总和、正数个数和最大值。这个例子沿用竞赛题常见的输入约束：题目保证会给出完整的 `n` 个数，并保证每个数及总和都在 `long long` 范围内；程序仍会检查实际读取是否成功。

```cpp
#include <iostream>
using namespace std;

int main()
{
    int n;
    if (!(cin >> n) || n <= 0)
    {
        cout << "invalid\n";
        return 0;
    }

    long long x;
    if (!(cin >> x))
    {
        return 0;
    }

    long long sum = x;
    long long maximum = x;
    int positiveCount = (x > 0);

    for (int i = 1; i < n; i++)
    {
        if (!(cin >> x))
        {
            return 0;
        }
        sum += x;
        if (x > 0)
        {
            positiveCount++;
        }
        if (x > maximum)
        {
            maximum = x;
        }
    }

    cout << "sum " << sum << '\n';
    cout << "positive " << positiveCount << '\n';
    cout << "maximum " << maximum << '\n';
    return 0;
}
```

输入：

```text
5
7 -2 0 12 4
```

输出：

```text
sum 21
positive 3
maximum 12
```

总和从 0 开始通常没问题，但“最大值”不能随手初始化为 0，否则全是负数时会得到一个输入中根本不存在的答案。这里先读第一个数作为初值，再处理剩余 `n - 1` 个数。因为空序列没有最大值，所以程序先检查 `n > 0`。

---

## 2.8 复杂度直觉：这段循环大概要跑多少次

复杂度的系统知识会在后续章节展开，本章先建立最重要的直觉：**不要只看有几层循环，要数循环体总共执行多少次。**

### 2.8.1 常见数量级

假设 `n` 是输入规模：

| 代码形态 | 执行次数直觉 | 常写作 |
| --- | ---: | --- |
| 从 1 遍历到 n | 约 n 次 | `O(n)` |
| 两层都从 1 到 n | 约 n² 次 | `O(n²)` |
| 每轮把 n 除以 2 | 约 log₂n 次 | `O(log n)` |
| 三层都从 1 到 n | 约 n³ 次 | `O(n³)` |

这里的大 O 只关心数据变大时的增长趋势，会忽略常数和较低次项。

### 2.8.2 一秒能跑多少次

在普通 C++ 竞赛环境中，`10^8` 次**极简单**操作常被当作一秒量级的粗略上限，但这不是保证：取模、除法、输入输出比加法慢，评测机和时限也各不相同。入门阶段可用下面的量级帮助排除明显错误：

- `n <= 10^5`：`O(n)` 通常轻松，`O(n²)` 往往不行；
- `n <= 5000`：`O(n²)` 已经要看常数和时限；
- `n <= 500`：简单 `O(n³)` 有时可行；
- `n <= 20`：甚至可能允许枚举所有子集，这会在以后学习。

若 `n = 100000`，两层各跑 `n` 次就是 `10^10` 次，不要等提交后的 TLE 来告诉你它太慢。

### 2.8.3 倍增循环通常是 O(log n)

下面的循环变量不断翻倍：

```cpp
#include <iostream>
using namespace std;

int main()
{
    int n;
    cin >> n;

    int count = 0;
    for (long long value = 1; value <= n; value *= 2)
    {
        count++;
    }

    cout << count << '\n';
    return 0;
}
```

输入 `100` 时，`value` 依次是 `1, 2, 4, 8, 16, 32, 64`，只循环 7 次，输出：

```text
7
```

每翻倍一次就更接近 `n`，执行次数约为 `log₂n`。循环变量使用 `long long`，这样即使 `n` 接近 `int` 上限，最后一次翻倍也不会先在 `int` 中溢出。所以判断复杂度既要看变量怎样变化，也要检查更新表达式会不会越过类型范围。

### 2.8.4 用数学公式替代循环

`1 + 2 + ... + n` 可以循环求和，也可以用公式 `n * (n + 1) / 2`。即使 `n` 只是 `10^9`，逐项循环也不现实，公式却只需常数次运算，而且此时结果仍能由 `long long` 保存。

```cpp
#include <iostream>
using namespace std;

int main()
{
    long long n;
    cin >> n;

    long long answer;
    if (n % 2 == 0)
    {
        answer = (n / 2) * (n + 1);
    }
    else
    {
        answer = n * ((n + 1) / 2);
    }

    cout << answer << '\n';
    return 0;
}
```

输入 `1000000`，输出：

```text
500000500000
```

先除以 2 再相乘，比直接计算 `n * (n + 1)` 更不容易溢出。不过如果数学结果本身超过 `long long`，仍需更大的类型。

---

## 2.9 综合示例：猜数字所需次数

假设答案位于 `[1, n]`。每次猜中间值，系统只告诉你答案更大、更小或猜中。下面用一个确定的答案模拟这个过程，展示条件、循环和复杂度如何连在一起。

```cpp
#include <iostream>
using namespace std;

int main()
{
    int n, target;
    cin >> n >> target;

    if (n < 1 || target < 1 || target > n)
    {
        cout << "invalid input\n";
        return 0;
    }

    int left = 1;
    int right = n;
    int guesses = 0;

    while (left <= right)
    {
        int middle = left + (right - left) / 2;
        guesses++;

        if (middle == target)
        {
            cout << "found " << middle << '\n';
            cout << "guesses " << guesses << '\n';
            break;
        }
        else if (middle < target)
        {
            left = middle + 1;
        }
        else
        {
            right = middle - 1;
        }
    }
    return 0;
}
```

输入：

```text
100 73
```

输出：

```text
found 73
guesses 6
```

每轮都会排除当前范围的一半，因此即使 `n` 很大，次数也增长得很慢。这就是二分查找的核心直觉。数组版本会在排序与查找章节正式学习，本例只把它当作控制流综合训练。

---

## 2.10 常见错误对照：CE、RE、WA、TLE

### 2.10.1 CE：编译错误      ~~//代码在自测跑通了再往上交！~~

**常见原因一：条件后误加分号。**

`if (x > 0);` 中的分号代表一个空语句，后面的花括号不再受 `if` 控制。它有时能编译，但逻辑已经错了；若后面还有 `else`，甚至可能直接 CE。

**常见原因二：漏括号、漏分号、符号不成对。**

出现大量报错时，先看编译器给出的**第一条**错误。后续报错往往只是第一处语法破坏引发的连锁反应。

**常见原因三：把 `=`、`==` 或中文符号写混。**

赋值与相等判断含义不同；中文括号、分号、引号也不是合法 C++ 标点。

### 2.10.2 RE：运行时错误

本章还没有数组，最常见的 RE 是整数除以 0 或取模 0：

```text
a / 0
a % 0
```

这两个操作都是未定义行为，可能直接导致程序异常退出。把“除数不为 0”写在短路条件左侧，或先用 `if` 单独检查。

另一个风险是有符号整数溢出。它属于未定义行为，不能假设程序总会得到某个固定错误值。

### 2.10.3 WA：答案错误

循环题的 WA 优先检查：

1. `=` 是否误写成 `==`，或反过来；
2. `&&` 与 `||` 是否写反；
3. 边界应该是 `<` 还是 `<=`；
4. 累加器、计数器是否初始化；
5. 多组数据时，变量是否在每组开始前重置；
6. `int` 是否装得下答案和中间结果；
7. 题目要求的输出格式是否完全一致。

测试边界时至少试：最小合法输入、最大合法输入、刚好跨过分支边界的值、0、负数（若允许）和一个普通值。

### 2.10.4 TLE：超出时间限制

循环题的 TLE 常见于三类原因：

- 忘记更新循环变量，写出了死循环；
- `continue` 跳过了 `while` 中的更新语句；
- 程序确实会结束，但复杂度太高，例如 `n = 10^5` 时仍写 `O(n²)`。

判断方法：先问“它能不能结束”，再问“结束前总共执行多少轮”。

### 2.10.5 关于报错

| 结果 | 检查 |
| --- | --- |
| CE | 括号、分号、花括号、中文标点、`else` 是否有匹配的 `if` |
| RE | 除数是否为 0、有符号溢出等未定义行为 |
| WA | 条件逻辑、循环边界、初始化、类型、输出格式 |
| TLE | 死循环、更新语句被跳过、枚举次数过多 |

---

## 2.11 代码习惯
1. `if` 和循环统一写花括号，即使当前只有一行；
2. 变量先初始化，累计和常从 `0` 开始，乘积常从 `1` 开始；
3. 条件复杂时拆成有名字的 `bool`，例如 `bool leap = ...;`；
4. 循环前明确区间是闭区间还是半开区间；
5. 能提前确定答案时用 `break`，但不要为了少跑几轮把代码写得晦涩难懂；
6. 写完后拿小数据手算一遍变量变化；

编译器警告不一定意味着程序编译错误，但它经常能抓到赋值写进条件、变量未使用、符号类型混合等问题。警告算是代码审查，不要主动忽略。

---

## 2.12 本章小结

本章看起来知识点很多，实际上可以压缩成下面这张路线图：

```text
变量与常量
    ↓ 通过运算符组合
表达式（产生一个值）
    ↓ 关系 / 逻辑表达式产生 bool
条件判断（选择一条路）
    ↓ 把判断与更新反复执行
循环（处理一批候选）
    ↓ 多个循环组合
枚举（系统尝试所有可能）
    ↓ 数总执行次数
复杂度入门
```

需要真正记住的关键点：

- 相等判断是 `==`，赋值是 `=`；
- 区间条件要用 `&&` 连接，C++ 不能写数学式 `1 <= x <= n`；
- `&&` 和 `||` 会短路，危险运算应放在保护条件之后；
- `for` 适合范围明确，`while` 适合结束条件明确，`do-while` 至少执行一次；
- `break` 结束当前循环，`continue` 跳过当前这一轮；
- `int * int` 可能在赋给 `long long` 前就溢出，必要时乘 `1LL`；
- 写循环必须同时想清楚起点、终点和更新；
- 数据范围决定可接受的枚举次数。

---

## 2.13 配套练习

请前往
沈阳化工大学在线判题平台：
[SYUCTOJ](https://oj.syuctacm.cn/contest/6ac5b694753773230ebbcb8f)
