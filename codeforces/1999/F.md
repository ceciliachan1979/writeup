# CodeForces contest 1999 (Div4) 第 F 題

問題係[呢度](https://codeforces.com/contest/1999/problem/F)

今次冇做成呢條題目，係估唔到 Div4 都要用 modular inverse 呢個技巧。

題目要求數有幾多個 subsequence such that median 係 1，其實就係數有幾多個 subsequence 1 嘅數目多過 0 嘅數目。

簡單行一轉，就知道條 string 有 $n$ 個 character，有 $p$ 個 $0$，有 $q$ 個 $1$，咁就會有 $C^q_xC^p_{k-x}$ 個 subsequences such that 有 $x$ 個 $1$ 同 $k-x$ 個 $0$。 

最後當然係加晒佢，所以我要知道所有可行嘅 $x$。

$x$ 當然係要 $\ge\lceil\frac{k}{2}\rceil$。

$x$ 當然係要 $\le k$﹐而且 $x \le q$

但咁係唔夠，因為你未必夠 $0$ 用。

我哋要埋 $k-x \le p$，即係 $x\ge k-p$。

搵到個 $x$ 嘅 range 同 formula for $x$ 之後，唯一嘅難題，就係咁複雜嘅 formula，個 $n$ 同埋 $k$ 又有成 200,000 咁大，點計？

啲 binomial coefficient 無非就係一堆 factorial，啲 factorial 其實可以 precompute。唯一比較麻煩嘅點樣搞除數，因為已經 mod 咗。

除數可以用 modular inverse 嚟做，要用返之前講過嘅 extended Euclidean algorithm。

如果搵到 $pa + qb = 1$，其實就知道 $qb \equiv 1 \mod p$，所以用 $b$ 同 $p$ 做 extended Euclidean algorithm，就會搵到 $q$

段 code 就係咁。

```py
from sys import stdin

def eea(p, q):
    r = p % q
    if r == 0:
        return (0, 1, q)
    else:
        (m, n, g) = eea(q, r)
        m1 = n
        m2 = m - n * ((p-r)//q)
        return (m1, m2, g)

def modular_inverse(i, mod):
    (m, _, _) = eea(i, mod)
    return m % mod

def binomial(factorials, inverses, mod, n, r):
    return factorials[n] * inverses[r] * inverses[n - r] % mod

def run(n, k, s):
    p = 0
    q = 0
    for v in s:
        if v == 1:
            q += 1
        else:
            p += 1
    product = 1
    mod = 10 ** 9 + 7
    factorials = [1]
    inverses = [1]
    for i in range(1, max(p, q) + 2):
        product *= i
        product %= mod
        factorials.append(product)
        inverses.append(modular_inverse(product, mod))
    result = 0
    for x in range(max((k + 1)//2, k - p), min(k, q) + 1):
        ones = binomial(factorials, inverses, mod, q, x)
        zeros = binomial(factorials, inverses, mod, p, k - x)
        counts = ones * zeros 
        counts %= mod
        result += counts
        result %= mod
    print(result)

def main():
    data = []
    for line in stdin:
        data.append(line.strip())
    num_test_case = int(data[0])
    line_number = 1
    for _ in range(num_test_case):
        n, k  = [int(x) for x in data[line_number].split()]
        line_number += 1
        s = [int(x) for x in data[line_number].split()]
        line_number += 1
        run(n, k, s)

if __name__ == "__main__":
    main()
```