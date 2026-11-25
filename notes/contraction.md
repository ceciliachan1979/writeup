上個周末溫書，高微，捽 Perplexity，又搵到一個有趣嘅 observation。

> 開波之前，戴返個頭盔先。成份嘢個目標係講 observation，唔係滴水不漏咁證。有 gap，唔齊，係正常嘅，我冇打算寫 Chapter 16 ⋯

原來 inverse function theorem 同 implicit function theorem 個 proof 係有好多類似嘅位。

首先，兩個 theorems 要證嘅都係 unique existence，對於 inverse function theorem，要求嘅係一個 domain (source) 嗰邊有一個附近唯一嘅點，對應 range (target 嗰邊) 一個足夠接近嘅點。

implicit function theorem 要求嘅係對應一個足夠近嘅 arg1 點，有個附近唯一嘅 arg2 點可以保持 output constant。[[1]](#note-setup)

留意到個 common theme 係附近有點唯一存在，咁點搞呢？原來兩邊都係用同一招，fixed point iteration。

留意 $f(x + h) \approx f(x) + Df(x)h$ 係一個 first order approximation[[2]](#note-approx)

先睇 inverse function theorem：

如果 $f(X) = Y$ ， $Y$ 係接近 $f(x)$ ，就可以咁諗：

$$
\begin{aligned}
f(X) &= Y && \text{[要求]} \\
f(x + h) &\approx f(x) + Df(x)h && \text{[用 first order approximation]} \\
Y - f(x) &\approx Df(x)h && \text{[想 solve } h\text{]} \\
Df(x)^{-1}\bigl(Y - f(x)\bigr) &\approx h && \text{[前提係有 inverse，用了]} \\
x + Df(x)^{-1}\bigl(Y - f(x)\bigr) &\approx x + h && \text{[完]}
\end{aligned}
$$

同樣道理，睇 implicit function theorem：

$$
\begin{aligned}
f(X, y+h_y) &\approx f(X, y) + D_yf(X,y)h_y && \text{[用 first order approximation，呢個睇法係當 } g(y) = f(X,y)\text{，唔理 } x \text{ 嘅 changes]} \\
0 &\approx f(X, y) + D_yf(X,y)h_y && \text{[放返要求入去]} \\
-f(X, y) &\approx D_yf(X,y)h_y && \text{[想 solve } h_y\text{]} \\
-D_yf(X,y)^{-1}f(X, y) &\approx h_y && \text{[又用前提有嘅 inverse]} \\
y - D_yf(X,y)^{-1}f(X, y) &\approx y + h_y = Y && \text{[完]}
\end{aligned}
$$

當然，呢啲全部都係 approximate，神奇嘅係，呢個 iteration 有 limit，而 limit 就係我哋要嘅結果。

Inverse function theorem:[[3]](#note-newton)

$$
x_{j+1} = T(x_j) = x_j + Df(x)^{-1}\bigl(Y - f(x_j)\bigr), \qquad x_0 = x
$$

Implicit function theorem:

$$
y_{j+1} = S(y_j) = y_j - D_yf(X,y)^{-1}f(X, y_j)
$$

首先，假設夢想成真，呢個 iteration 真係有 limit，會點呢？

$$
L = L + Df(x)^{-1}\bigl(Y - f(L)\bigr) \;\Rightarrow\; Y = f(L)
$$

$$
L = L - D_yf(X,y)^{-1}f(X, L) \;\Rightarrow\; f(X, L) = 0
$$

爽！我只需要證明 limit exists。[[4]](#note-limit)

寫到呢度先 ⋯

---

證呢個 limit exists 嘅重要工具係叫做 contraction mapping，呢個 mapping 有一個特點：

假設 $u, v$ 係某個 open set 裡面，contraction mapping 要求：

$$
\|T(u) - T(v)\| \le q\,\|u - v\|
$$

其中 $0 \le q < 1$ independent of $u$ and $v$ 。[[5]](#note-ball)

Turn out 我哋個 iteration 係 contraction map，但唔好理住，先睇吓 contraction map 有咩著數先：

**Fix point uniqueness:**

假設 $T(a) = a$, $T(b) = b$

$$
\|T(a) - T(b)\| \le q\,\|a - b\|
$$

$$
\|a - b\| \le q\,\|a - b\|
$$

咁就好明顯 $a = b$ ，如果唔係嘅話 $q \ge 1$ 係 contradict $q < 1$ 。[[6]](#note-local)

**Cauchy：**

$$
\begin{aligned}
\|T^{n+1}(u) - T^n(u)\| &\le q\,\|T^n(u) - T^{n-1}(u)\| \le \dots \le q^n\|T(u) - u\| \\
\|T^{n}(u) - T^{n-1}(u)\| &\le \dots \le q^{n-1}\|T(u) - u\| \\
&\;\;\vdots \\
\|T^{m+1}(u) - T^{m}(u)\| &\le \dots \le q^m\|T(u) - u\|
\end{aligned}
$$

加晒佢，用 triangle inequality，咁就得到

$$
\|T^{n+1}(u) - T^m(u)\| \le (q^n + \dots + q^m)\,\|T(u) - u\| \le \frac{q^m}{1-q}\,\|T(u) - u\|
$$

加埋 $\mathbb{R}^n$ 係 complete 所以就會 converge[[7]](#note-complete)

寫到呢度先 ⋯

---

舖到呢度，好明顯我下一步係要證個 iteration map 係 contraction。呢度個重點係一個叫做 mean value inequality。

$\mathbb{R} \to \mathbb{R}$ 嘅 function 有個好舒服嘅 Lagrange Mean Value Theorem

There exists $c$ such that $f(b) - f(a) = f'(c)(b-a)$

係 $\mathbb{R}^m \to \mathbb{R}^n$ 世界呢，係冇咁爽嘅

我地只有

$$
\|T(b) - T(a)\| \le M\,\|b - a\|
$$

個樣似十足 Lagrange，除咗中間變咗 $\le$ 之外，所以佢叫做 Mean Value Inequality。[[8]](#note-lagrange)

$M$ 係咩？ $M$ 係 $\sup \|DT(z)\|$ for all $z$ in the line segment $[a, b]$ [[9]](#note-convex)

唔好理點解啱住，假設 $M$ 可以 $\le q$ ，咁就正了，好明顯證到 $T$ (或者 $S$) 係 contraction mapping。

計一次 $DT$ 同 $DS$ 先

$T(z)$ 係 $z + Df(x)^{-1}\bigl(Y - f(z)\bigr)$ ，咁 $DT(z)$ 就係 $I - Df(x)^{-1}Df(z)$ ，留意

$$
DT(x) = I - Df(x)^{-1}Df(x) = 0
$$

$S(z)$ 係 $z - D\_yf(X,y)^{-1}f(X,z)$ ，咁 $DS(z)$ 就係 $I - D\_yf(X,y)^{-1}D\_yf(X,z)$ ，留意

$$
DS(y) = I - D_yf(X,y)^{-1}D_yf(X,y) = 0
$$

呢兩個 $= 0$ 好重要，要做到個 $M$ ，我可以由呢一點開始做一個波，保持裡面個 $\|D\|$ 係細過 $1/2$ ，咁就可以 claim 喺呢個波裡面任意兩點，條線段上面都有 $\|D\|$ 細過 $1/2$ ，用得 mean value inequality。[[10]](#note-smallball)[[11]](#note-selfmap)

寫到呢度先 ⋯

---

寫到呢度係最後一集，解釋埋最後一個黑盒，mean value inequality。

其實 mean value inequality 冇乜特別，只係夾硬 project 落條線到，整到佢係 scalar function，再用 mean value theorem 屈一點存在，最後用 norm 定義屈做 inequality ⋯

Define

$$
u = \frac{f(b) - f(a)}{\|f(b) - f(a)\|}
$$

[注：係 output 嘅 direction！呢個 $u$ 只對某一對 $[a,b]$ 成立]

$$
\begin{aligned}
\langle f(b) - f(a),\, u\rangle &= \langle D(\langle f,u\rangle)(z),\, b - a\rangle && \text{[MVT on } g(x) = \langle f(x),u\rangle \text{ give } z \text{ (depending on } u\text{) in } [a,b]\text{]} \\
\|f(b)-f(a)\| &= \langle u^{\top}Df(z),\, b-a\rangle && \text{[左手邊係簡單 vector geometry，右手邊係 chain rule]} \\
&= \langle u,\, Df(z)(b-a)\rangle && \text{[呢步用咗 matrix multiplication 係 associative]} \\
&\le \|u\|\,\|Df(z)(b-a)\| && \text{[呢步用咗 Cauchy–Schwarz, dot product} \le \text{norm product]} \\
&= \|Df(z)(b-a)\| && \text{[} u \text{ has norm 1]} \\
&\le M\,\|b - a\| && \text{[最後用 matrix operator norm 嘅定義]}
\end{aligned}
$$

咁就砌到 mean value inequality。[[12]](#note-mvi)[[13]](#note-regularity)

---

## 註

*以下註釋由 Claude 補寫。正文係作者原文；作者唔係唔知呢啲細節，只係為咗突出 idea，冇逐樣寫出嚟。*

<a id="note-setup"></a>

**1.** （Claude）正式假設：inverse function theorem 入面， $f$ 係 $C^1$ （即 $Df$ 連續），而且 $Df(x)$ 可逆。implicit function theorem 入面， $f(x,y)=0$ ， $f$ 係 $C^1$ ，而且 $D\_yf(x,y)$ 可逆。符號方面，細楷 $x, y$ 係 base point，大楷 $X, Y$ 係附近嘅點。留意兩邊角色唔同：inverse 嗰邊 $X$ 係要搵嘅未知數， $Y$ 係已知；implicit 嗰邊 $X$ 係已知， $Y$ 先係要搵嘅未知數。

<a id="note-approx"></a>

**2.** （Claude）first order approximation 有 error： $f(x+h) = f(x) + Df(x)h + o(\|h\|)$ 。下面條 derivation 將 error 扔咗再 solve $h$ ，所以得出嚟嘅 $x+h$ 唔會剛好係答案，只係一個更好嘅估計，要 iterate。 $f$ 係 $C^1$ 係用嚟保證呢個 error 夠細，令 iteration 有機會收斂。

<a id="note-newton"></a>

**3.** （Claude）留意 $Df(x)^{-1}$ 一直固定喺 base point $x$ ，唔係每步 update（即係 simplified Newton，唔係 full Newton）。正正因為咁， $DT(z) = I - Df(x)^{-1}Df(z)$ 先會喺 $z=x$ 時等於 $0$ 。Full Newton 係另一個故事，收斂係 quadratic，唔係呢度用嘅 contraction 嗰套。

<a id="note-limit"></a>

**4.** （Claude）呢步要 $f$ continuous 先可以將 limit 搬入 $f$ 入面。另外 $\Rightarrow$ 其實可以寫成 $\Leftrightarrow$ ：因為 $Df(x)^{-1}$ 可逆（injective）， $T(L)=L$ 同 $f(L)=Y$ 係等價，所以 $T$ 嘅 fixed point 就係方程嘅解。implicit 嗰邊同理。

<a id="note-ball"></a>

**5.** （Claude）下面實際用嘅唔係任意 open set，而係 base point 附近一個 closed ball $B$ ，而且要 $T(B)\subset B$ （見下面關於 self-map 嘅註）。之後 $q$ 會取 $1/2$ 。

<a id="note-local"></a>

**6.** （Claude）Uniqueness 係 local：只係喺呢個 ball $B$ 入面唯一，唔係全局唯一。呢個正正就係 theorem 講嘅「附近唯一」。例如 $f(x)=x^2$ 喺 $x=1$ 附近可逆，但 $f(-1)=f(1)$ ，只係 $-1$ 唔喺附近。

<a id="note-complete"></a>

**7.** （Claude）呢度仲需要兩樣。第一，closed ball 係 $\mathbb{R}^n$ 入面嘅 closed set，所以都係 complete，limit 會留喺 ball 入面，唔會走出界。第二，iteration 每一步都要留喺 ball 入面，否則 contraction 嘅 bound 就冇保證，呢點由 $T(B)\subset B$ 嚟（見下面關於 self-map 嘅註）。另外嗰個 sum 實際係 $\sum_{k=m}^{n} q^k$ ，所以 $m\to\infty$ 時 $\to 0$ ，sequence 先係 Cauchy。

<a id="note-lagrange"></a>

**8.** （Claude）向量版連「揾到一點 $z$ 令 $f(b)-f(a)=Df(z)(b-a)$ 」都唔成立。例子： $f(t)=(\cos t,\sin t)$ 喺 $[0,2\pi]$ 上面， $f(b)-f(a)=0$ ，但 $\|f'(t)\|=1$ 處處非零。所以只能用 sup 嚟兜住，而唔係單一點。

<a id="note-convex"></a>

**9.** （Claude）要求成條線段 $[a,b]$ 都喺 $T$ 嘅 domain 入面，ball 係 convex，所以滿足。 $M$ 有限係因為 $DT$ 連續，喺 compact 嘅線段上面有 sup。 $\|DT(z)\|$ 係 operator norm。

<a id="note-smallball"></a>

**10.** （Claude） $DT(z)=I-Df(x)^{-1}Df(z)$ 係 $z$ 嘅連續函數（因為 $f$ 係 $C^1$ ， $Df(x)^{-1}$ 固定），喺 $z=x$ 時係 $0$ ，所以存在 $r>0$ 令 $\|z-x\|\le r$ 時 $\|DT(z)\|\le 1/2$ 。呢個 $r$ 就係個「波」嘅半徑。

<a id="note-selfmap"></a>

**11.** （Claude）Contraction 仲欠一樣： $T$ 要將 ball 打返入自己度（ $T(B)\subset B$ ）。用 $q=1/2$ ，若 $\|z-x\|\le r$ ，則 $\|T(z)-x\|\le\|T(z)-T(x)\|+\|T(x)-x\|\le \tfrac12 r+\|Df(x)^{-1}(Y-f(x))\|$ 。所以只要 $\|Df(x)^{-1}(Y-f(x))\|\le r/2$ ，即係 $Y$ 夠近 $f(x)$ ，就得。呢個就係「 $Y$ 要足夠接近」嘅真正作用。implicit 嗰邊：要 $DS(z)$ 細，需要 $X$ 同 $z$ 都夠近 base point（由 $D\_yf$ 連續），所以 $r$ 要同時控制兩者；self-map 方面， $f(X,y)$ 當 $X$ 夠近 $x$ 時會近 $f(x,y)=0$ ，令 $\|S(y)-y\|\le r/2$ ，原理一樣。

<a id="note-mvi"></a>

**12.** （Claude）幾個細節。(1) $f(b)=f(a)$ 時 $u$ 冇定義，但呢個情況不等式顯然成立。(2) MVT 實際用喺 $g(t)=\langle f(a+t(b-a)),u\rangle$ ， $t\in[0,1]$ ， $g'(t)=\langle u, Df(a+t(b-a))(b-a)\rangle$ ，而 $z$ 就係 $a+t^\*(b-a)$ 。(3) 呢度 $\|\cdot\|$ 要係 Euclidean norm，因為用咗 inner product 同 Cauchy–Schwarz，其他 norm 要另外嘅論證。(4) $\|Df(z)\|$ 係 operator norm， $\|Df(z)(b-a)\|\le\|Df(z)\|\,\|b-a\|$ 就係佢嘅定義。

<a id="note-regularity"></a>

**13.** （Claude）呢個 writeup 淨係證咗 existence 同 uniqueness。theorem 嘅完整版仲包括 inverse（或者 implicit 嗰個函數）本身係 $C^1$ ，呢部分要另外證，呢度冇涵蓋。
