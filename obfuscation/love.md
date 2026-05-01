# Love

愚人節又到了！呢個係 [you-fool](you-fool.md) 嘅姊妹篇，同樣 output "I love 2025 April Fools!"，但用完全唔同嘅數學。

```py
data = [92, 33, 125, 191, 163, 88, 198, 13, 218, 37, 132, 110, 18, 155, 222, 97, 143, 130, 81, 235, 0, 64, 26, 173, 88]
v = 1
for k in data:
    for _ in range(k):
        v = ((v << 1) ^ v ^ ((v >> 7) * 27)) & 255
    print(chr(v), end="")
```
[Try it online!](https://tio.run/##JU7LCoMwELz7FUMPJSkemsRnUX@k1CLVVhGiiAj9ejtr9zDszGRmM3/XfvJu39tmbVDintsQzoUwNibkhpCQZpkwARJruLhUCF8bcyWIFTNiLaVcvEhaHL2MJdbR455E3BMa6VH6CDYeNcF7WjBi8JBv3AJwRHqKtDT@06lR/2UZySi1oShgNGry@uBVhVTjAptqjTNsHB@ReRn8ql79ojYdovNteTrpYN9/)

Output:

```txt
I love 2025 April Fools!
```

代碼看起來很清楚 — 初始化 `v = 1`，對每一個 `k` 做 `k` 次某個 bitwise operation，然後 output `chr(v)`。注意 `v` 係一路 carry 落去嘅，每個字符係基於前一個字符嘅結果。但點解嗰堆數字會變成 "I love 2025 April Fools!" 呢？

## $\text{GF}(2^8)$

$\text{GF}(2^8)$ 係一個有 256 個元素嘅 finite field (Galois field)。 $256 = 2^8$ ，啱啱好可以 encode 一個 byte，呢點係整個 obfuscation 嘅核心。

Field 嘅元素可以用 degree < 8 嘅 polynomial over GF(2) (即係 coefficient 只有 0 同 1) 嚟表示，等價於一個 8-bit binary number。例如：

$$
x^6 + x^4 + x^3 + x + 1 \leftrightarrow 01011011 \leftrightarrow 91
$$

加法就係 XOR — 因為 GF(2) 裡面 $1 + 1 = 0$ 。

乘法就係 polynomial multiplication，然後 mod 一個 irreducible polynomial。我哋用同 AES 加密算法一樣嘅 irreducible polynomial：

$$
p(x) = x^8 + x^4 + x^3 + x + 1
$$

即係話

$$
x^8 \equiv x^4 + x^3 + x + 1 \pmod{p(x)}
$$

用二進制嚟表示， $x^4 + x^3 + x + 1 = 00011011 = 27$ 。呢個就係段 code 入面嗰個 magic number 27 嘅來源。

## Multiplication by $x$

In $\text{GF}(2^8)$ ，乘以 $x$ 等價於 left shift by 1。如果 shift 完個 degree 去到 8（即係原本嘅 bit 7 係 1），就要 reduce mod $p(x)$ ，即係 XOR 返 27。

呢個操作喺 AES specification 入面叫做 `xtime`：

$$
\text{xtime}(v) = (v \ll 1) \oplus \begin{cases} \text{0x1B} & \text{if } v \geq 128 \\ 0 & \text{otherwise}\end{cases}
$$

用 code 嚟寫就係 `((v << 1) ^ ((v >> 7) * 27)) & 255`。其中 `v >> 7` 只會係 0 或 1，所以 `(v >> 7) * 27` 就係個 conditional reduction。`& 255` 係因為 left shift 之後可能有第 9 個 bit，要 mask 走佢。

舉兩個例子。取 $v = 83$ （ $01010011 = x^6 + x^4 + x + 1$ ），bit 7 係 0：

$$
\text{xtime}(83) = 10100110 = 166
$$

最高次只去到 $x^7$ ，唔使 reduce。

再取 $v = 212$ （ $11010100 = x^7 + x^6 + x^4 + x^2$ ），bit 7 係 1，shift 完 $x^7$ 變成 $x^8$ ，需要 XOR 27：

$$
\text{xtime}(212) = 10101000 \oplus 00011011 = 10110011 = 179
$$

用 polynomial 嚟驗算：

$$
(x^7 + x^6 + x^4 + x^2) \cdot x = x^8 + x^7 + x^5 + x^3
$$

代入 $x^8 = x^4 + x^3 + x + 1$ ，注意 GF(2) 入面 $x^3 + x^3 = 0$ ：

$$
= x^7 + x^5 + x^4 + x + 1 = 10110011 = 179 \checkmark
$$

## Multiplication by 3

$3$ 嘅二進制係 $11$ ，對應嘅 polynomial 係 $x + 1$ 。所以

$$
v \times 3 = v \times (x + 1) = v \times x + v \times 1 = \text{xtime}(v) \oplus v
$$

展開之後就係段 code 入面嘅

```
((v << 1) ^ v ^ ((v >> 7) * 27)) & 255
```

沿用 $v = 212$ 嘅例子：

$$
212 \times 3 = \text{xtime}(212) \oplus 212 = 179 \oplus 212 = 10110011 \oplus 11010100 = 01100111 = 103
$$

同 [abc](abc.md) 嘅概念一樣 — 嗰度係 $\text{GF}(5^2)$ 裡面乘以 generator $x + 2$ ，呢度係 $\text{GF}(2^8)$ 裡面乘以 generator $x + 1$ 。

## Primitive Root

$\text{GF}(2^8)^\times$ (即係除咗 0 以外嘅 multiplicative group) 有 $255 = 3 \times 5 \times 17$ 個元素，而且係 cyclic group。

$g = 3$ （即 $x + 1$ ）係呢個 group 嘅 generator (primitive root)，即 $g$ 嘅 power 可以遍歷所有 255 個非零元素。

要驗證呢一點，只需要 check $g^{255/p} \ne 1$ for each prime factor $p$ ：

$$
\begin{align*}
3^{85}  &= 189 \ne 1 \\
3^{51}  &= 12  \ne 1 \\
3^{15}  &= 53  \ne 1
\end{align*}
$$

## Discrete Logarithm

既然 $g = 3$ 係 generator，每一個非零元素 $a \in \text{GF}(2^8)^\times$ 都可以寫成 $a = 3^k$ ，其中 $k \in \{0, 1, \ldots, 254\}$ 。呢個 $k$ 就係 $a$ 嘅 discrete logarithm。

> 留意 0 唔喺 multiplicative group 入面，所以 null character (ASCII 0) 冇 discrete logarithm，encode 唔到。但呢個對於 printable text 完全冇影響。

## Differential Encoding

最簡單嘅方法係儲每個字符嘅 absolute discrete log，每次由 $v = 1$ 開始重新計。但段 code 用咗一個更巧妙嘅方法 — **differential encoding**：`v` 係一路 carry 落去嘅，`data` 入面儲嘅係相鄰字符 discrete log 嘅差。

設 message 嘅字符為 $c\_0, c\_1, \ldots$ ，佢哋嘅 discrete logarithm 為 $\ell\_i = \log\_3(\text{ord}(c\_i))$ 。

$$
d_i = \begin{cases} \ell_0 & \text{if } i = 0 \\ \ell_i - \ell_{i-1} \pmod{255} & \text{if } i > 0 \end{cases}
$$

段 code 每次乘 $d\_i$ 次 3，即係 $v \gets v \cdot 3^{d\_i}$ 。因為

$$
3^{d_0 + d_1 + \cdots + d_i} = 3^{\ell_i}
$$

所以 $v$ 嘅值就係第 $i$ 個字符嘅 ASCII value。

例如：

| 字符 | ASCII | $\ell\_i$ | $d\_i$ |
|------|-------|---------|-------|
| I    | 73    | 92      | 92    |
| (空格) | 32  | 125     | 33    |
| l    | 108   | 250     | 125   |
| o    | 111   | 186     | 191   |
| v    | 118   | 94      | 163   |
| e    | 101   | 182     | 88    |
| (空格) | 32  | 125     | 198   |
| 2    | 50    | 138     | 13    |
| 0    | 48    | 101     | 218   |
| 2    | 50    | 138     | 37    |
| 5    | 53    | 15      | 132   |
| (空格) | 32  | 125     | 110   |
| A    | 65    | 143     | 18    |
| p    | 112   | 43      | 155   |
| r    | 114   | 10      | 222   |
| i    | 105   | 107     | 97    |
| l    | 108   | 250     | 143   |
| (空格) | 32  | 125     | 130   |
| F    | 70    | 206     | 81    |
| o    | 111   | 186     | 235   |
| o    | 111   | 186     | 0     |
| l    | 108   | 250     | 64    |
| s    | 115   | 21      | 26    |
| !    | 33    | 194     | 173   |
| \n   | 10    | 27      | 88    |

留意兩個連續嘅 'o' — 差係 0，所以乘 0 次，`v` 唔變，自然 output 同一個字。

以下呢段 code 可以自動將任意 string 轉換成 differential discrete logarithm：

```py
def gf_mul(a, b, mod=0x11B):
    result = 0
    while b:
        if b & 1:
            result ^= a
        a <<= 1
        if a & 0x100:
            a ^= mod
        b >>= 1
    return result

g = 3
log_table = [None] * 256
v = 1
for i in range(255):
    log_table[v] = i
    v = gf_mul(v, g)

goal = "I love 2025 April Fools!\n"
logs = [log_table[ord(c)] for c in goal]
diffs = [logs[0]]
for i in range(1, len(logs)):
    diffs.append((logs[i] - logs[i-1]) % 255)

print("data = %s" % diffs)
v = 1
for k in diffs:
    for _ in range(k):
        v = ((v << 1) ^ v ^ ((v >> 7) * 27)) & 255
    print(chr(v), end="")
```
[Try it online!](https://tio.run/##XZFfT4MwFMXf@ymOJDOtYQa24BIzSPTBxBe/wOyWMgojY5QAQ/30eMv@MO1T77n99Zz2Vj/tzpTzvk90iizdHI4FVy5iFweThN6377@KZwZatW6ORYsQ3lB@7fJCIz717MpTxLiHPyo31DqEuuoKy2UI/5ZURJKZ5/2llQUpyFWMEUUXtNbtsS7PDoxlFG3OCpNtWhVTtBCrD1NqiQfMgifWwXKpqZEjJ0qVmeazIDi/7sqtOkkn80G0zPlPOheZIBOjChKddwI6jZk3C/BS1XmBN2OK5u6zdGyExrqPV5o64VshYd231t1eI1mSp@nlZLPypPwfz3dR6JLbtjjnHJhHVVW6TPjQWeUSU5x2U18KTGCfxRjFKlvuJKpVZDJpHOoMuLj5jL11G9TT/VbbjAn2YhyIhTjvaHjwBdZUr4c6irAQ9pcXQtAYyXxATvbbXc074YLiho4jWN//Ag)

同 [you-fool](you-fool.md) 類似嘅玩法，但完全唔同嘅數學 — 嗰邊用 periodic continued fraction，呢邊用 finite field discrete logarithm。同 [1st-april](1st-april.md) 同 [abc](abc.md) 一樣都係 Galois field 嘅應用，但呢次用嘅係 $\text{GF}(2^8)$ — 同 AES 加密算法用嘅係同一個 finite field 😄
