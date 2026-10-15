# pumpkin

南瓜戴上面具會點呢？

```py
pumpkin = 62401149
print("".join(chr(pumpkin % ord(mask)) for mask in "mask"))
```

[Try it online!](https://tio.run/##K6gsycjPM9YtqCyo/P@/oDS3IDszT8FWwczIxMDQ0MSSq6AoM69EQ0lJLys/M08jOaNIA6ZIVSG/KEUjN7E4W1NTIS2/SAHEVABKKIEYSpqa//8DAA "Python 3 (PyPy) – Try It Online")

Output 係 `BOO!`

呢個唔係我嘅原創，作者係 Perplexity ⋯

再捽 Perplexity，又搵到幾個更加好玩嘅 examples

```py
pumpkin = 241750740
print("".join(chr(pumpkin % ord(mask)) for mask in "maple"))
```

[Try it online!](https://tio.run/##K6gsycjPM9YtqCyo/P@/oDS3IDszT8FWwcjE0NzUwNzEgKugKDOvRENJSS8rPzNPIzmjSAOmSlUhvyhFIzexOFtTUyEtv0gBxFQASijlJhbkpCppav7/DwA "Python 3 (PyPy) – Try It Online")

Output 係 `LATTE`

呢啲 cases 有趣嘅位係個 moduli 唔係 coprime。

## Chinese Remainder Theorem

首先釋一下先，上次個 `mask` 其實係 standard Chinese Remainder Theorem (CRT)。個 idea 係

Given

$$
\begin{align*}
x &\equiv a_1 \pmod{m_1} \\
x &\equiv a_2 \pmod{m_2} \\
&\vdots \\
x &\equiv a_n \pmod{m_n}
\end{align*}
$$

用 CRT，可以搵到 $x \bmod M$ ，其中 $M = m_1 m_2 \cdots m_n$ 。

要用 CRT，前提要求係 $\gcd(m_i, m_j) = 1$ for $i \ne j$ （i.e. pairwise coprime）。

要搵 $x$ ，我哋需要呢啲類似 basis 嘅 gadget

$$
\begin{align*}
e_1 &\equiv 1 \pmod{m_1}, & e_1 &\equiv 0 \pmod{m_j} \text{ for } j \ne 1 \\
e_2 &\equiv 1 \pmod{m_2}, & e_2 &\equiv 0 \pmod{m_j} \text{ for } j \ne 2 \\
&\vdots
\end{align*}
$$

假設搵到呢啲 gadgets，個答案就可以好簡單咁寫做

$$
x = a_1 e_1 + a_2 e_2 + \cdots + a_n e_n \pmod{M}
$$

睇住啲 gadgets 嘅 definition，唔難諗點解個 proposed answer match 晒所有要求。

咁點搵啲 gadget 呢，用 Bézout，因為 coprime，所以有 $\alpha$ 同 $\beta$ 使得

$$
\alpha m_1 + \beta m_2 m_3 \cdots m_n = 1
$$

於是

$$
\begin{align*}
\beta m_2 m_3 \cdots m_n &\equiv 1 \pmod{m_1} \\
\beta m_2 m_3 \cdots m_n &\equiv 0 \pmod{m_j} \text{ for } j \ne 1
\end{align*}
$$

所以 $e_1 = \beta m_2 m_3 \cdots m_n$ 。咁就快速搵到啲 gadgets。

留意 coprime 好重要，冇 coprime 就冇 Bézout $= 1$ ，冇 gadget 冇得搞。

## Non-coprime moduli

咁如果啲 moduli 唔係 coprime 呢？

要去 solve 呢啲 non-coprime moduli case，個 key observation 係假如你知道 $x \equiv k \pmod{pq}$ ，其實你就知道 $x \equiv k \pmod{p}$ ，將 $x$ 寫做 $x = pqr + k$ 就好明顯了。

假設有兩個 non-coprime moduli

$$
\begin{align*}
x &\equiv a \pmod{m_1} \\
x &\equiv b \pmod{m_2} \\
\gcd(m_1, m_2) &= d
\end{align*}
$$

呢個位我哋可以 explicit 啲咁寫

$$
\begin{align*}
x &= a + t m_1 && (t \in \mathbb{Z}) \\
a + t m_1 &= b + u m_2 && (u \in \mathbb{Z}) \\
t m_1 &= b - a + u m_2 && \text{(note the asymmetry here)}
\end{align*}
$$

Bézout gives

$$
p m_1 + q m_2 = d
$$

如果 $\frac{b-a}{d}$ 唔係一個 integer，就會咁：

$$
\begin{align*}
x &\equiv a \pmod{d} \\
x &\equiv b \pmod{d} \\
0 &\equiv b - a \not\equiv 0 \pmod{d}
\end{align*}
$$

然後個 solution set 就冇 solution 了。所以假設有 solution， $\frac{b-a}{d}$ 就一定係 integer，然後一齊乘

$$
p \cdot \frac{b-a}{d} \cdot m_1 + q \cdot \frac{b-a}{d} \cdot m_2 = b - a
$$

比較返 $t m_1 = b - a + u m_2$ 就會見到 $t$ 可以係 $p \cdot \frac{b-a}{d}$ ，

咁 $x$ 就可以係

$$
x = a + t m_1 = a + p \cdot \frac{b-a}{d} \cdot m_1
$$

依家搵到一個 $x$ ，要搵所有 $x$ ，假設我哋有一對 $x_1, x_2$ 都得（i.e. $x_1 \equiv x_2 \equiv a \pmod{m_1}$ 同埋 $x_1 \equiv x_2 \equiv b \pmod{m_2}$ ），咁就會得到

$$
\begin{align*}
x_1 - x_2 &\equiv 0 \pmod{m_1} \\
x_1 - x_2 &\equiv 0 \pmod{m_2}
\end{align*}
$$

咁好明顯， $x_1 - x_2 \equiv 0 \pmod{\operatorname{lcm}(m_1, m_2)}$ 。

咁即係話

$$
x \equiv a + p \cdot \frac{b-a}{d} \cdot m_1 \pmod{\operatorname{lcm}(m_1, m_2)}
$$

咁樣就可以將兩條 non-coprime 嘅 modular equation 變成一條，只要仲有 non-coprime pair 就不停咁做嘅話，最後就只能變返晒全部都 coprime，之後就可以用真 CRT 了。
## Design

有咗上面嘅 merge，我哋就可以反過嚟設計自己嘅 pumpkin。目標係：揀一個 mask，再揀想要嘅 output text，搵個 `pumpkin` 令到 `pumpkin % ord(mask[i]) == ord(text[i])` 對所有 $i$ 都成立。

即係話每個 character 都係一條 congruence，其中 modulus 係 mask character 嘅 ASCII value，remainder 係 output character 嘅 ASCII value。我哋由 $x \equiv 0 \pmod{1}$ 開始，逐條 merge 入去。

```py
from math import gcd

def mod_inverse(a, m):
    # extended Euclid, so this also works on Python < 3.8 (e.g. TIO's PyPy)
    old_r, r = a % m, m
    old_s, s = 1, 0
    while r:
        q = old_r // r
        old_r, r = r, old_r - q * r
        old_s, s = s, old_s - q * s
    return old_s % m

def pumpkin_generator(mask, text):
    if len(mask) != len(text):
        raise ValueError("Mask and text must have the same length")

    pumpkin, modulus = 0, 1

    for mask_char, output_char in zip(mask, text):
        n = ord(mask_char)
        remainder = ord(output_char)

        if n == 0:
            raise ValueError("Mask cannot contain a null character")

        if remainder >= n:
            raise ValueError(
                f"{output_char!r} cannot be produced by {mask_char!r}: "
                "the output code must be smaller than the modulus"
            )

        d = gcd(modulus, n)
        difference = remainder - pumpkin

        if difference % d:
            raise ValueError("No solution: incompatible remainders")

        reduced_n = n // d

        t = (
            0 if reduced_n == 1
            else (
                (difference // d)
                * mod_inverse(modulus // d, reduced_n)
            ) % reduced_n
        )

        pumpkin += modulus * t
        modulus *= reduced_n
        pumpkin %= modulus

    return (
        f"pumpkin = {pumpkin}\n"
        f'print("".join(chr(pumpkin % ord(mask)) for mask in {mask!r}))'
    )
```

段 code 就係上面 merge 嘅直接翻譯，最後會 return 一段完整嘅 pumpkin program，而唔係淨係個數。 `modulus` 係到目前為止嘅 lcm， `pumpkin` 係滿足曬頭先所有條件嘅解。 `d` 就係 $\gcd(m_1, m_2)$ ， `t` 就係 $p \cdot \frac{b-a}{d}$ ，不過因為我哋直接用 $m_1 / d$ 嘅 modular inverse，所以 $t$ 只需要 mod $\frac{m_2}{d}$ ，而 `modulus * reduced_n` 就係新嘅 lcm。

呢度有幾個 constraints，亦都係段 code 會 raise error 嘅位：

1. **Remainder 一定要細過 modulus。** $x \bmod m$ 嘅結果只會喺 $0$ 到 $m - 1$ 之間，所以 output character 嘅 ASCII value 一定要 $<$ mask character 嘅 ASCII value。呢條係最嚴格嘅限制，因為 mask 同 output 都係 character。
2. **Remainders 要 compatible。** 如果兩個 moduli 有 $\gcd(m_1, m_2) = d$ ，咁 $a \equiv b \pmod{d}$ 先有 solution，即係上面講嘅 $d \mid (b - a)$ 。
3. **Modulus 唔可以係 0。** 即係 mask 唔可以有 null character，不過呢點基本上唔會撞到。

Constraint 1 就係點解 target 通常係大楷。小楷嘅 ASCII 係 97 到 122，大楷係 65 到 90，標點符號 (例如 `!` 係 33) 就更細。咁只要 mask 用小楷，output 用大楷或者標點，就自動滿足 $\text{output} < \text{mask}$ ，所以 `BOO!` 同 `LATTE` 先會咁揀。如果反過嚟，用 `mask` 去 output `boo!` ，咁 `a` (97) 就要 output `o` (111) ，根本做唔到，段 code 會直接 raise error。

Constraint 2 就係 `maple` 有趣嘅位。`p` (112) 同 `l` (108) 嘅 gcd 係 4，所以佢哋嘅 remainders 要滿足 $a \equiv b \pmod{4}$ 。我哋要嘅 output 係 `T` 同 `T` (兩個都係 84)，所以啱啱好 compatible。如果要 `T` 同 `U` (84 同 85)，就冇 solution 了。

試一下：

```py
print(pumpkin_generator("mask", "BOO!"))
print(pumpkin_generator("maple", "LATTE"))
```

[Try it online!](https://tio.run/##hVTBTtwwEL3nK4ZUiISGLYhLhbqVWolDpbZwQD1VWpl4snFx7NR2oFvEt2/HTtZJughySTLz/GbmzYzbjau1Oj9pN@1mu62MbqBhrgbRtNo4WJc8SThW0Gi@EuoejcWMFdDkFwnQ8wbwj0PFkcNlV0rBC7AaXC0sMElfD9rcWdAKrkMc@ADni/eQ4WK9gJsvV0eWHNebPHBpyVemAANLYHAIDUWJdku8ZD8r4DTYHmohEUyfhH9@kzcQwLt3YKJ5wkmvHnBC4OP/MAO/7TF2wNiAMeg6owYH5dUL0nZNeyfUao0KDXPaZA2zdwU4EmQQR1QgUQV7DgfL8DNxB24mLMIPJju8NIZI0m@EBqZ4IIKmsw5qdo@kKYJlDXqWtavTPAkkQxqFb1AnO1/EaQFnvbPSBnz0VVkzX37n2s6FHxAK/op2P2f/KK@l4Vk8mo/5YsMEtdsMkAnlkNBQOHFQJiPnC8WWTCntoNTKETf1XnVSgqdkpUOTzonHDD4uQb0SYOYNiqSPk5QPzNMu@i1Ca0jCkib5dgOPsXbCXEC6R5T6fvRUlDnHvlPEYhsmJWXnaqZC04bGzCkmNXGSktYsG3AFqFFuLqoKDaoS/QDHyk92bZ8pMwEfAn9N@u@aNlV2Tmh1QdNQ6qZlTtz6rdrFsVPpDQZxVn44lF8yPvoc2eZan/atikdodWd@lJTOfnuySQ0@RL6HOJ7dRLuh99hijDc/lpMc0ZU804FBTHi7jFt0DC66o235DM3u7GE8m0wvjbHEKt1Bl/A4fD79VONYVEetEcplabr4pYXKytpkkT3uY57HrfY7HOaURjTPj5Khqp5l/3pKPTQtIP18dXWQ5vlLwFaiR379dHNz6aHb7T8 "Python 3 (PyPy) – Try It Online")

Output 係

```
pumpkin = 62401149
print("".join(chr(pumpkin % ord(mask)) for mask in 'mask'))
pumpkin = 241750740
print("".join(chr(pumpkin % ord(mask)) for mask in 'maple'))
```

呢兩段就係最頭嗰兩個 program。

Pumpkin 嘅 solution 係 mod lcm，所以所有 moduli 嘅 lcm 加上去之後都係 solution，段 code 出嘅係最細嗰個。