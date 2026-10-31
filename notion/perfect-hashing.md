---
title: "Storing a Sparse Table with O(1) Worst Case Access Time"
source: "https://app.notion.com/p/9735a10b550040a1b02f9e4fbe69c1ee"
---

[https://www.cs.princeton.edu/courses/archive/fall09/cos521/Handouts/storingasparse.pdf](https://www.cs.princeton.edu/courses/archive/fall09/cos521/Handouts/storingasparse.pdf)

## 為甚麼讀這篇論文？

這份論文應該是 Perfect Hashing 的始祖。Perfect Hashing 指的是一個永遠不會有 hash code collision 的 hash table，代價是 key 必須全部都預先知道。

## 奇怪的 hash function

一開始，論文莫名奇妙的考慮這個 function，先講講 domain 和 range。

$U$ 是 $\lbrace 1, 2, …, m\rbrace$ 是 universal set of keys，我們沒有那麼多 keys，但 keys 必須來自於 $U$ 。

$p = m + 1$ 必須是一個 prime number，這個條件問題不大，你總是可以把 $m$ 調大的。

$W$ 是一個 subset of $U$ ，是所有 keys，為甚麼不命名為更明顯的 $K$ 呢，不知道。

$r$ 是 $W$ 的 size，為甚麼要叫 $r$ 也是不知。

$S$ 是 $\lbrace 0, 1, \cdots, s - 1\rbrace$ 是 range。

$k$ 是一個隨意的整數， $0 \le k \lt  p$ 。[^nonzero-multiplier]

有了這些，就可以開始定義這個 function 了，它是 

$$
f_k: U \to S, f(x) = (kx \text{ mod } p) \text{ mod } s
$$

我不知道這個 function 是從哪裡來的，論文一開始就有。

## 數手指

把它當成 hash function 來看的話，我們想知道它的 collision 怎麼樣，所以定義了這個集合

$$
B(k, j) = \lbrace x | x \in W, f_k(x) = j\rbrace
$$

$B$ 大概是指 bucket 吧，整個集合的定義的人話說法就是使用 $f_k$ 這個 hash function 時 hash value 是 $j$ 的 bucket 有多大。

$\binom{\left|B(k, j)\right|}{2}$ 可以理解為有多少個 pair 在 $f_k$ 下 hash 到 $j$ 的 bucket 去。

我們可以 sum over all $j$ 去算 hash 到同一個 bucket (不論哪一個 bucket) 的 pairs 有幾個。

我們可以再 sum over all $k$ 去算 hash 到同一個 bucket (不論哪一個 bucket，也不管哪一個 hash function，只要是同一個）的 triplet 有幾個。

因為 $W$ 是 $U$ 的隨意 subset，其實真要算這個 sum 它只能是暴力做，還好，我們其實只想要一個 upper bound。

這個是一個很好的例子，說明可以用不同的方法數 (i.e. enumerate) 有時會有有簡化了問題。

一個 bucket 裡面有一對 $\lbrace x, y\rbrace$ 的 condition 是

$$
\begin{aligned}
kx\text{ mod } p \text{ mod } s = ky\text{ mod } p \text{ mod } s
\end{aligned}
$$

用減法，可以簡化[^modular-subtraction]

$$
\begin{aligned}
& kx \text{ mod } p \text{ mod s } - ky \text{ mod } p \text{ mod s } \\
=&k(x-y) \text{ mod } p \text{ mod } s \\
=& 0
\end{aligned}
$$

所以說 $k(x-y) \text{ mod } p$ 的值其實必須是一個 $s$ 的倍數，注意，因為減號，所以負倍也可以。

比如說 $p$ 是 7， $s$ 是 4，符合條件的 $k(x-y)$ 有幾個呢？明顯 $4$ 可以，但 $3$ 也可以，因為 $3 =-4 \text{ mod } 7$ ，其它的 $1, 2, 5, 6$ 都不成。0 是不可以的，因為 $x \ne y$ 。

由於 p 是 prime，p 的正倍數和負倍數不會是同一個數值的，所以符合條件的 有 $2\lfloor \frac{p}{s}\rfloor$ 個

有了這些算術後，我們可以數了，首先想像把 $W$ 裡的所有 $(x, y)$ pair 都計一下 $(x - y) \text { mod } p$ ，然後把它們放到不同的格子裡。

這時 $p$ 是 prime 便十分有用了，對應於每一個 $(x - y) \text{ mod } p$ 的值，對應於每一個被允許的 $k(x - y) \text { mod } p$ 的值，其實都必然會有一個唯一的 $k$ ，那是因為 $\mathbb{Z}_p$ 是 finite field。

我們有幾個格子，每個格子對應的都有 $2\lfloor \frac{p}{s}\rfloor$ 個 $k$ 會讓這個格子的 pair 符合要求，所以我們總共有 $\frac{r(r - 1)}{2} \times 2 \lfloor \frac{p}{s} \rfloor$ 個符合的 triplets。[^collision-count]

所以我們 conclude $\sum\limits\_{k = 1}^{p-1} \sum\limits\_{j = 0}^{s} \binom{B(k, j)}{2} \lt  \frac{(p-1)r^2}{s}$ 。[^bucket-notation]

留意我們有 $p - 1$ 個不同的 k 加起來，總數是 $\frac{(p-1)r^2}{s}$ ，那麼總是會有一個 $k$ 它的 sum 是小於 $\frac{r^2}{s}$ ，這就證了 Lemma 1。

## Corollaries

相比起 Lemma 1，這些 Corollaries 就溫柔很多。

Corollary 1 主要是 expand binomial coefficient，炸開後得到 $\frac{B(B-1)}{2}$ ，要變成 $B^2$ ，乘 2 再加 B 就可以了。留意 $s \ge r$ ，所以 $\frac{r^2}{s} \le r$ 。

Corollary 2 更容易，只是 substitute $s = r^2$ ，然後小於 $1$ 的整數只能是 $0$ ，換句話說，沒有 collision pairs，那就真是 1 to 1 了。

## 如何使用這些 **Corollaries**

首先， $W$, $r$, $m$ 和 $p$ 都是 given 的，我們唯一可選的是 $s$ ，我們先試試 $s = r$ 看看。

Corollary 1 告訴我們會有 collision，但這些 collision 的 bucket size sum of square 會小於 $3r$ 。

換句話說，每一個 bucket 我們可以用 size 的 square 去 store 它，這樣最終也只是小於 $3r$ 。

就麼辦，利用 Corollary 2，如果我願意用 square size 去 store，我可以保證沒有 collision。

這就用兩層的 hash 做到沒有 collision 的效果了。

## 例子

這份論文提供的例子實在太好 ⋯ 非常方便驗證是否理解正確。

例如 $2$ 這個數值，第一層 hash 會用 $k = 2$ ，所以 hash 後是 $2 \times 2 \text{ mod } 31 \text{ mod } 6 = 4$ ，然後它果然就到了 $4$ 號 bucket 的位置， $10$ 。

這裡我們會看到這個 bucket 的 size 是 $2$ ， $k'$ 是 $1$ ，所以再一次 hash 是 $1 \times 2 \text{ mod } 31 \text{ mod } 4 = 2$ ，果然到了 $4$ 號 bucket 的第 $2$ 個是 $2$ 。

> 留意論文中例子使用的 $\text{ mod }$ 取值是 $1,2,\cdots,n$ 而非更 conventional 的 $0,1,2, \cdots$ ，而且 bucket 也是從 1 開始數。想想其實也是也第 0 個轉到最後面，並不影響使用。

## 還有呢？

我論文只讀到這裡，大概明白了算法就夠了，後面的包括放寛 table size 的要求去加速找 $k$ ，和一些其它和 reference 有關的內容，我就沒讀了，因為這文章講的都是 1984 年的 algorithm，到今天其實已經是 outdated，讀它主要是見證一下歷史上第一個 perfect hash 是如何做的。

Cecilia,

2nd, June, 2024

[^nonzero-multiplier]: **條件澄清：** `k = 0` 可以定義 hash function，但會把所有 keys hash 到 0。後面「0 是不可以的」和利用 finite field 的非零乘法計數，只能在 `1 <= k < p` 的範圍下使用。

[^modular-subtraction]: **勘誤：** 先 mod `p` 再 mod `s` 並不保持這個減法等式。反例是 `p = 7, s = 4, k = 1, x = 1, y = 5`：兩個 hash values 都是 1，但 `k(x-y) mod p mod s = 3`，不是 0。正確的出發點是設 `a = kx mod p` 和 `b = ky mod p`，collision 等價於 signed difference `a-b` 是 `s` 的倍數；這個 difference 可以是負數，不能先把它變成 modulo `p` 的非負 residue 再直接 mod `s`。

[^collision-count]: **勘誤：** 上述 signed-difference 條件只給出候選 residues，不能把候選個數當成實際 collision 個數。對每對不同的 keys，能導致 collision 的非零 `k` 的個數至多是 `2 floor((p-1)/s)`；有些候選 residues 不會產生 collision。這裡需要的是 upper bound，而不是「exactly 有這麼多個」的等式。

[^bucket-notation]: **記號勘誤：** `B(k, j)` 是 set，binomial coefficient 的上項應是 cardinality `|B(k, j)|`。Buckets 的 index 是 0 到 `s - 1`，所以內層 summation 的上限應是 `s - 1`，不是 `s`。
