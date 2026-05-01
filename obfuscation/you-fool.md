# You fool

愚人節嘅一個小作品

```py
import math

v1 = 68325343582907243
v2 = 1130193560657982
v3 = 4683795212406213031697706359870653

for i in range(90):
    v4 = int((v1 + math.sqrt(v3))/v2)
    v1 = v4 * v2 - v1
    v2 = (v3 - v1 * v1)//v2
    print(chr(v4), end="")
```

[Try it online!](https://tio.run/##JY5JDsMgDEX3nMLqCjoFbIZQqYepOiWLkJQipJ4@dRJvLH8/f//pV7ox0Tz3wzTmAsOtdEJUA1fwLaEjS67FqANaEhVZNoa0ieS89i7EFkUlVi3TITo0aLVHRsj4GIL25GLLzZEQrzFDD32CfEvvp4xaXQRwVcsGfSpS8t/DGuH8/eQiKynVVFQbtWRidA8c48Tjpi6RGFyVZWdUwyfrbsqL6b3Lslp1hGd6XHc7Jeb5Dw)

代碼這樣寫，明顯就是用來愚弄人的。更有意思的是它的 output！

```txt
you fool
you fool
you fool
you fool
you fool
you fool
you fool
you fool
you fool
you fool

```

沒錯，就是 `you fool` 10 次。

當然，這個小程序的奧秘就在那些奇怪的大數字上，改一改這些數字，就可以換個說法了，比如這個我就很喜歡 💕

```py
v1 = 274837438206617830390466951
v2 = 8215139504484131940708082
v3 = 75987464439523495044906458403225069926697833317272557
```

呢篇文主要係會講返呢個 program 係點 work ⋯ 個 idea 係 [periodic continued fraction](https://en.wikipedia.org/wiki/Periodic_continued_fraction) 就係 quadratic irrational。個 loop 無非只係 periodic 咁 output 嘢啫，啱晒。

個 program 純粹係計 $\frac{v\_1 + \sqrt{v\_3}}{v\_2}$ 嘅 partial quotients。

# Decoder

首先， $v\_4 = \lfloor\frac{v\_1 + \sqrt{v\_3}}{v\_2}\rfloor$ ，所以 $v\_4 + \frac{1}{x} = \frac{v\_1 + \sqrt{v\_3}}{v\_2}$ 

下一步就要計下一個 partial quotient，個 idea 改咗啲 $v$ 嘅值，改到 $\frac{v\_1' + \sqrt{v\_3'}}{v\_2'} = x$ 

咁都係基本 simplify。

$$
\begin{align*}
v_4 + \frac{1}{x} &= \frac{v_1 + \sqrt{v_3}}{v_2} \\
\frac{1}{x} &= \frac{v_1 + \sqrt{v_3}}{v_2} - v_4\\
&= \frac{v_1 + \sqrt{v_3} - v_2 v_4}{v_2} \\
&= \frac{\sqrt{v_3} - (v_2 v_4 - v_1)}{v_2} \\
x &= \frac{v_2}{\sqrt{v_3} - (v_2 v_4 - v_1)} \\
&= \frac{v_2(\sqrt{v_3} + (v_2 v_4 - v_1))}{(\sqrt{v_3} - (v_2 v_4 - v_1))(\sqrt{v_3} + (v_2 v_4 - v_1))} \\
&= \frac{v_2(\sqrt{v_3} + (v_2 v_4 - v_1))}{v_3 - (v_2 v_4 - v_1)^2} \\
&= \frac{v_2 v_4 - v_1 + \sqrt{v_3}}{\frac{v_3 - (v_2 v_4 - v_1)^2}{v_2}} \\
\end{align*}
$$

不難看出段 code 同條數係 match，要特別留意第二行寫個 `v2` 係 depends on update 完之後嘅 `v1`。

```
v1 = v2 * v4 - v1
v2 = (v3 - v1 * v1)//v2
```

# Divisibility

淨返最後一個 details，就係點解個 `v2` 係 integer division，關於呢一點，我哋可以用一個好簡單嘅 induction 證到其實 $v\_3 - (v\_2 v\_4 - v\_1)^2$ 係 multiple of $v\_2$ ，係呢個情況下就可以 make sure integer divison 冇問題。

首先係 initial condition，對於一個任意嘅 quadratic irrational，其實 $v\_3 - (v\_2 v\_4 - v\_1)^2$ 未必一定係 multiple of $v\_2$ 

隨便一個就知道唔得

$$
\frac{1 + \sqrt{8}}{5}
$$

不難算出

$$
\begin{align*}
v_4 &= 0 \\
v_3 - (v_2 v_4 - v_1)^2 &= 8 - (5 \times 0 - 1)^2 \\
&= 7
\end{align*}
$$

然後 7 當然不能被 5 整除。

我哋可以用 $v\_1' = v\_2v\_1,v\_2' = v\_2^2,v\_3' = v\_2^2v\_3$ 嚟解決呢個問題。

首先個分數數值冇變。

$$
\begin{align*}
& \frac{v_1' + \sqrt{v_3'}}{v_2'} \\
=& \frac{v_2v_1 + \sqrt{v_2^2v_3}}{v_2^2} \\
=& \frac{v_2v_1 + v_2\sqrt{v_3}}{v_2^2} \\
=& \frac{v_1 + \sqrt{v_3}}{v_2}
\end{align*}
$$

當分數數值不變， $v_4$ 當然也不變。

但係依家有 divisibility 了， $v\_3' - (v\_2' v\_4 - v\_1')^2 = v\_2^2 v\_3 - (v\_2^2 v\_4 - v\_2v\_1)^2$ 係 divisible by $v\_2' = v\_2^2$ 。

有咗 initial divisibility 後，後面就容易了。

$$
\begin{align*}
& v_3' - (v_2' v_4' - v_1')^2 \\
=& v_3 - (v_2' v_4' - (v_2 v_4 - v_1))^2 \\
=& v_3 - (v_2' v_4')^2 + 2v_2' v_4'(v_2 v_4 - v_1) - (v_2 v_4 - v_1)^2 \\
=& v_3 - (v_2 v_4 - v_1)^2 + 2v_2' v_4'(v_2 v_4 - v_1) - (v_2' v_4')^2 \\
\text{divisible by }& v_2' = \frac{v_3 - (v_2 v_4 - v_1)^2}{v_2}
\end{align*}
$$

咁就證明到只要一開始係 divisible，就之後都會係。

# Constructor

依家我哋知道咗段 code 係點樣計 partial quotient，仲有一個問題，就係原本個大數值係點嚟。好明顯係要由我想要 output 嗰條 string 度嚟。

## Convergents

要 construct 呢三個數，首先要講 convergents。

對於 continued fraction $[a\_0; a\_1, a\_2, \ldots]$ ，佢嘅 convergents $\frac{p\_n}{q\_n}$ 可以用以下 recurrence 計：

$$
\begin{align*}
p_{-2} &= 0, & q_{-2} &= 1 \\
p_{-1} &= 1, & q_{-1} &= 0 \\
p_n &= a_n \cdot p_{n-1} + p_{n-2}, & q_n &= a_n \cdot q_{n-1} + q_{n-2}
\end{align*}
$$

Convergents 嘅一個重要性質係，如果我哋知道 partial quotients 直到 $a_n$ ，而之後嘅「尾巴」 嘅值係 $x$ ，咁成個 continued fraction 嘅值就係：

$$
[a_0; a_1, \ldots, a_n, x] = \frac{x \cdot p_n + p_{n-1}}{x \cdot q_n + q_{n-1}}
$$

呢條 formula 嘅意思係：無論個「尾巴」 $x$ 係咩，我哋都可以用 convergents 去將佢 combine 返做一個 fraction。

> **證明**：用 induction。Base case $n = 0$ ： $[a\_0, x] = a\_0 + \frac{1}{x} = \frac{a\_0 x + 1}{x} = \frac{x \cdot p\_0 + p\_{-1}}{x \cdot q\_0 + q\_{-1}}$ ✓
>
> Inductive step：假設 $[a\_1; \ldots, a\_n, x] = \frac{x \cdot p'\_n + p'\_{n-1}}{x \cdot q'\_n + q'\_{n-1}}$ （呢度 $p', q'$ 係由 $a\_1$ 開始嘅 convergents）。
>
> 咁 $[a\_0; a\_1, \ldots, a\_n, x] = a\_0 + \frac{1}{[a\_1; \ldots, a\_n, x]} = a\_0 + \frac{x \cdot q'\_n + q'\_{n-1}}{x \cdot p'\_n + p'\_{n-1}} = \frac{a\_0(x \cdot p'\_n + p'\_{n-1}) + (x \cdot q'\_n + q'\_{n-1})}{x \cdot p'\_n + p'\_{n-1}}$
>
> 因為 $p\_n = a\_0 \cdot p'\_n + q'\_n$ 同 $q\_n = p'\_n$ （由 recurrence 嘅性質），所以呢個就等於 $\frac{x \cdot p\_n + p\_{n-1}}{x \cdot q\_n + q\_{n-1}}$ 。 ∎

## Step 1：Solve the Periodic Part

假設我哋想要嘅 loop 嘅 ASCII values 係 $[l\_0, l\_1, \ldots, l\_{n-1}]$ 。

Periodic 嘅意思係 $x = [l\_0; l\_1, \ldots, l\_{n-1}, x]$ ——個「尾巴」就係 $x$ 自己。

用上面嘅 convergent formula：

$$
x = \frac{x \cdot p_{n-1} + p_{n-2}}{x \cdot q_{n-1} + q_{n-2}}
$$

Cross-multiply 同 rearrange：

$$
\begin{align*}
x(x \cdot q_{n-1} + q_{n-2}) &= x \cdot p_{n-1} + p_{n-2} \\
q_{n-1} x^2 + (q_{n-2} - p_{n-1}) x - p_{n-2} &= 0
\end{align*}
$$

呢個就係一條 quadratic equation！用 quadratic formula：

$$
x = \frac{-(q_{n-2} - p_{n-1}) + \sqrt{(q_{n-2} - p_{n-1})^2 + 4 q_{n-1} p_{n-2}}}{2 q_{n-1}}
$$

（取正根）

所以 $x = \frac{P + \sqrt{D}}{Q}$ 其中：

$$
\begin{align*}
P &= p_{n-1} - q_{n-2} \\
D &= (q_{n-2} - p_{n-1})^2 + 4 q_{n-1} p_{n-2} \\
Q &= 2 q_{n-1}
\end{align*}
$$

到呢度，如果冇 prefix（即係成段 text 都係 repeating），就已經完成了。

## Step 2：Back-Propagate the Prefix

如果有 prefix $[c\_0, c\_1, \ldots, c\_{m-1}]$ ，我哋需要搵一個 $y$ 令到：

$$
y = [c_0; c_1, \ldots, c_{m-1}, x]
$$

其中 $x = \frac{P + \sqrt{D}}{Q}$ 係我哋剛剛計到嘅 periodic part。

呢度嘅 trick 係由後面 propagate 返去前面。如果我哋知道 $x = \frac{P + \sqrt{D}}{Q}$ ，而 $y = c + \frac{1}{x}$ ，咁 $y$ 嘅 $(P', D', Q')$ 係幾多？

$$
\frac{1}{x} = \frac{Q}{P + \sqrt{D}} = \frac{Q(\sqrt{D} - P)}{D - P^2} = \frac{-P + \sqrt{D}}{\frac{D - P^2}{Q}}
$$

（rationalize 個 denominator）

令 $k = \frac{D - P^2}{Q}$ ，咁：

$$
y = c + \frac{1}{x} = c + \frac{-P + \sqrt{D}}{k} = \frac{ck - P + \sqrt{D}}{k}
$$

所以：

$$
\begin{align*}
P' &= ck - P \\
Q' &= k \\
D' &= D
\end{align*}
$$

注意 $D$ 不變——呢個 make sense，因為加一個 prefix 唔會改變 quadratic irrational 嘅 discriminant。

## $k$ 係整數嗎？

呢度 $k = \frac{D - P^2}{Q}$ 要係整數，integer division 先至冇問題。

對於 Step 1 得出嘅 $(P, D, Q)$ ，我哋可以驗證 $D - P^2 = (q\_{n-2} - p\_{n-1})^2 + 4q\_{n-1}p\_{n-2} - (p\_{n-1} - q\_{n-2})^2 = 4q\_{n-1}p\_{n-2}$ ，而 $Q = 2q\_{n-1}$ ，所以 $k = 2p\_{n-2}$ ，確實係整數。

之後每一步嘅 back-propagation 其實同 continued fraction extraction 係 inverse operation，所以 divisibility 會一直保持（同上面 Divisibility 一節嘅 induction 一樣嘅道理）。

## 完整 Code

```py
import math

def get_symbolic_coeffs(prefix, loop, limit):
    p_coeffs = [ord(c) for c in prefix]
    l_coeffs = [ord(c) for c in loop]
    
    # Step 1: Convergents for the periodic part
    a_prev, b_prev = 0, 1
    a_curr, b_curr = 1, 0
    for x in l_coeffs:
        a_next = x * a_curr + a_prev
        b_next = x * b_curr + b_prev
        a_prev, b_prev = a_curr, b_curr
        a_curr, b_curr = a_next, b_next
    
    # Step 2: Solve quadratic for the fixed point
    A_q, B_q, C_q = b_curr, (b_prev - a_curr), -a_prev
    D = B_q**2 - 4 * A_q * C_q
    P, Q = -B_q, 2 * A_q
    
    # Step 3: Back-propagate the prefix
    for c in reversed(p_coeffs):
        k = (D - P**2) // Q
        P = c * k - P
        Q = k
    
    print(f"v1 = {P}")
    print(f"v3 = {D}")
    print(f"v2 = {Q}")
    
    # Verify by extracting
    output = []
    p, q = P, Q
    for _ in range(limit):
        a_i = (p + int(math.sqrt(D))) // q
        output.append(chr(a_i))
        p = a_i * q - p
        q = (D - p**2) // q
    return "".join(output)

print(get_symbolic_coeffs("April Fools! ", "you fool\n", 90))
```

[Try it online!](https://tio.run/##fVRNj5swEL3zK6b0glOSzUcvjdTD7kY9J1qpl90KGTDEDbGNMVFQ1d@ejm1gk6YqByPmPc@beR6jOrOXYnW58KOS2sCRmn0Q5KyAkpmk6Y6prHiWZJIVRRMpzQp@jqGSUuHKj9yQdQD4qJ4CX@FV6jzKCBRSQwZcgN/1w/Gq//BsVs9yy0d4MUzBYg3PUpyYLpkwjWObPQPFNJc5z0BRbRyfJih0iiF1bxSYx7DokazV2iL2jcgihrlDbLaz0@7r8t34TYKdDZLPMOkzwKdeZCSl16R0IKW3pLvCbuu54v1Vpi8h7lXunFmu4UVWJwZ1S3NNDZoxuIN@sxyU5MJve0zqGJ7s8pzUmDntpaK@pmkvTmKYXrW4QSrumkyWyPiMLWIeXDGHg7cx7JAxdYmXHr6rcrWGJ5odpkpLRUtqmD8@NxPjGbjzR1WmG5ZHwzCR99M4oE60wSq2WAyBhwfYjdgWsQzVDxYeo7ayw3s1SqMXURGeFhj/tf0dktvwyoY3d@GlDe@GcN/Xd5y9ooO0AzwWTTPDRekg2RrV2nF49XOMl8S6bX0aW01cq1SULLq@QH4AuG1T4QhZeXsXZ02tTbQhxPVcj1SvNKNKMYG3aK8j3EzIiCs3PhxdqdEVNcbrwUc1@OhzamZaLSAMZz9xaCKfngSBN@Jf/4LwEbEKvklZNR8gjCHsZIsdyupN4NeXOVZzufwB)

# 同 [Help!](help.md) 嘅比較

| | Help! (循環小數) | You fool (Continued Fraction) |
|---|---|---|
| 數學基礎 | 循環小數 = 有理數 | Periodic CF = 二次無理數 |
| 參數 | 2 個大數 $(a, b)$ | 3 個大數 $(v\_1, v\_2, v\_3)$ |
| 運算 | 乘法 + `divmod` | 加減乘 + `sqrt` |
| 數學深度 | 小學 | Lagrange's theorem |
| 字符集 | 需要預先定 base（base 256 只能 encode ASCII） | 無限制，partial quotient 可以係任意正整數，中文都得 |

兩種方法嘅 obfuscation 效果都好，因為最終都只係幾個大數加一個簡單嘅 loop。但 continued fraction 版本多咗一個 `sqrt`，令到段 code 更加神秘 🧙。
