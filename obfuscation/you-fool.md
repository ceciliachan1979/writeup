# You fool

愚人節嘅一個小作品

```py
import math

v1 = 68325343582907243
v2 = 1130193560657982
v3 = 4683795212406213031697706359870653

for i in range(90):
    v4 = int((v1 + math.sqrt(v3))/v2)
    v1 = v2 * v4 - v1
    v2 = (v3 - v1 * v1)//v2
    print(chr(v4), end="")
```

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

個 program 純粹係計 $\frac{v_1 + \sqrt{v_3}}{v_2}$ 嘅 partial denominators。

首先， $v_4 = \lfloor\frac{v_1 + \sqrt{v_3}}{v_2}\rfloor$ ，所以 $v_4 + \frac{1}{x} = \frac{v_1 + \sqrt{v_3}}{v_2}$ 

下一步就要計下一個 partial denominators，個 idea 改咗啲 $v$ 嘅值，改到 $\frac{v_1' + \sqrt{v_3'}}{v_2'} = x$ 

咁都係基本 simplify。

$$\begin{align*}
v_4 + \frac{1}{x} &= \frac{v_1 + \sqrt{v_3}}{v_2} \\
\frac{1}{x} &= \frac{v_1 + \sqrt{v_3}}{v_2} - v_4\\
&= \frac{v_1 + \sqrt{v_3} - v_2 v_4}{v_2} \\
&= \frac{\sqrt{v_3} - (v_2 v_4 - v_1)}{v_2} \\
x &= \frac{v_2}{\sqrt{v_3} - (v_2 v_4 - v_1)} \\
&= \frac{v_2(\sqrt{v_3} + (v_2 v_4 - v_1))}{(\sqrt{v_3} - (v_2 v_4 - v_1))(\sqrt{v_3} + (v_2 v_4 - v_1))} \\
&= \frac{v_2(\sqrt{v_3} + (v_2 v_4 - v_1))}{v_3 - (v_2 v_4 - v_1)^2} \\
&= \frac{v_2 v_4 - v_1 + \sqrt{v_3}}{\frac{v_3 - (v_2 v_4 - v_1)^2}{v_2}} \\
\end{align*}$$

不難看出段 code 同條數係 match，要特別留意第二行寫個 `v2` 係 depends on update 完之後嘅 `v1`。

```
v1 = v2 * v4 - v1
v2 = (v3 - v1 * v1)//v2
```

淨返最後一個 details，就係點解個 `v2` 係 integer division，關於呢一點，我哋可以用一個好簡單嘅 induction 證到其實 $v_3 - (v_2 v_4 - v_1)^2$ 係 multiple of $v_2$ ，係呢個情況下就可以 make sure integer divison 冇問題。

首先係 initial condition，對於一個任意嘅 quadratic irrational，其實 $v_3 - (v_2 v_4 - v_1)^2$ 未必一定係 multiple of $v_2$ 

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

我哋可以用 $v_1' = v_2v_1,v_2' = v_2^2,v_3' = v_2^2v_3$ 嚟解決呢個問題。

首先個分數數值冇變。

$$
\begin{align*}
& \frac{v_1' + \sqrt{v_3'}}{v_2'} \\
=& \frac{v_2v_1 + \sqrt{v_2^2v_3'}}{v_2^2} \\
=& \frac{v_2v_1 + v_2\sqrt{v_3'}}{v_2^2} \\
=& \frac{v_1 + \sqrt{v_3'}}{v_2}
\end{align*}
$$

當分數數值不變， $v_4$ 當然也不變。

但係依家有 divisibility 了， $v_3' - (v_2' v_4 - v_1')^2 = v_2^2 v_3' - (v_2^2 v_4 - v_2v_1)^2$ 係 divisible by $v_2' = v_2^2$ 。

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

依家我哋知道咗段 code 係點樣計 partial denominator，仲有一個問題，就係原本個大數值係點嚟。好明顯係要由我想要 output 嗰條 string 度嚟。

依家個 problem to solve 就變成呢種 equation， $a,b,c$ 係已知 coefficient，求 $x$ 。

 $x = a + \frac{1}{b + \frac{1}{c + x}}$ 

其實只係 elementary arithmetic

$$
\begin{align*}
x &= a + \frac{1}{b + \frac{1}{c + x}} \\
&= a + \frac{1}{\frac{b(c+x) + 1}{c + x}} \\
&= a + \frac{x + c}{bx + bc + 1}
\end{align*}
$$

唔難睇出呢個方法每一次可以將最裡面嗰個 fraction 最少一層，咁有限層最終都會 solve 到變成一個 fraction of linear equation，最後掟過左手邊變成 quadratic 就完了。

以下呢段 code 係一個自動嘅 solver，輸入隨意嘅 string 就可以得到數字了

```py
import math

#
# Denest a continued fraction of this form
# 1/(a + (bx + c)/(dx + e))
#
# to
#
# (dx + e)/((ad + b)x + (ae + c))
#
def denest(a, n, d):
    (b, c) = n
    (d, e) = d
    return ((d, e), (a * d + b, a * e + c))

#
# Solve the equation x = (bx + c)/(dx + c) 
#
# to the surd form (-B + sqrt(delta))/ 2A
# 
def solve(n, d):
    (b, c) = n
    (d, e) = d
    A = d
    B = e - b
    C = -c
    delta = (B ** 2 - 4 * A * C)
    return (-B, delta, 2 * A)

def construct(goal):
    n = (0, 1)
    d = (1, 3)

    g = [ord(x) for x in goal]
    first = g[0]

    s = list(g[1:])
    s.append(first)

    start = s.pop()
    n = (0, 1)
    d = (1, start)

    while len(s) > 0:
        (n, d) = denest(s.pop(), n, d)

    (p, d, q) = solve(n, d)
    p += (first * q)

    return (p, q, d)

goal = "You fool\n"

(v1, v2, v3) = construct(goal)
length = len(goal)
print("v1 = %s" % v1)
print("v2 = %s" % v2)
print("v3 = %s" % v3)
print("length = %s" % length)

for i in range(length * 10):
  v4 = int((v1 + math.sqrt(v3))/v2)
  v1 = v4 * v2 - v1
  v2 = (v3 - v1 * v1)//v2
  print(chr(v4), end="")
```
