# Median of Medians

這篇寫 deterministic linear-time selection algorithm，通常叫做 **median of medians**。重點是把 rounding、constant、base case threshold 這些煩人的 bookkeeping 講清楚。

## Algorithm

問題：在一個 size $n$ 的 array 中找第 $k$ 小的 element。

1. 把 array 分成每組 5 個。
2. 找每組的 median。
3. Recursively 找這些 medians 的 median，叫它 $p$ 。
4. 用 $p$ 做 pivot，partition 之後只 recurse 到其中一邊。

重點是 $p$ 保證是一個好的 pivot，不會太偏。

## Proportion argument

考慮 input size 為 $n$ ，先忽略不滿 5 個的 leftover group。

分成 groups of 5，有大約 $n/5$ 組。取每組的 median，再取這些 medians 的 median $p$ 。

數一數有多少 elements 保證 $\le p$ ：

- 至少一半的 group medians $\le p$ ，即大約 $n/10$ 組。
- 每組 median $\le p$ 的 group 裡有至少 3 個 elements $\le p$ （median 是第三大）。
- 所以至少 $3n/10$ 個 elements $\le p$ 。

By symmetry，至少 $3n/10$ 個 elements $\ge p$ 。

所以 partition 之後較大的那邊最多大約 $7n/10$ 。

## 嚴格的 counting

上面忽略了 floors 和 ceilings。嚴格來說，較大的 recursive side 的 size 最多是

$$
\left\lceil \frac{7n}{10} \right\rceil + c_1
$$

為甚麼可以這樣寫？因為：

- Grouping 最多剩 4 個 leftovers。
- Full groups 的數目是 $\lfloor n/5 \rfloor$ 。
- 取 median of medians 可能多算一組。
- 每個 floor/ceiling 最多差 1。

這些全部是不隨 $n$ 增長的常數，統一塞進一個 $c_1$ 就好。Textbook 常寫 $\lceil 7n/10 \rceil + 6$ ，但具體數字不重要，重點是它不隨 $n$ 長大。

## Recurrence

設 $T(n)$ 為 worst-case running time。Algorithm 做三件事：找 groups of 5 的 medians、recursively 找 median of medians、partition 後 recurse 一邊。Non-recursive work 是 linear，bounded by $an$ 。

我們想寫：

$$
T(n) \le T(\lfloor n/5 \rfloor) + T(\lfloor 7n/10 \rfloor + c_1) + an
$$

但這其實已經假設了 $T$ 是 monotone。為甚麼？因為 partition 之後實際 recurse 的 size 是某個 $s \le \lfloor 7n/10 \rfloor + c\_1$ ，要從 $T(s)$ 推到 $T(\lfloor 7n/10 \rfloor + c\_1)$ ，就需要 $T$ 不會因為 input 變大而變小。

但 $T(n)$ 對於很小的 $n$ 可能不是 monotone（可能 $T(6) < T(5)$ 之類）。所以這條 recurrence 要成立，需要先修正 $T$ 。

## Monotone envelope $T'$

定義 monotone envelope：

$$
T'(n) = \max_{1 \le m \le n} T(m)
$$

性質：
- $T'$ 是 monotone nondecreasing。
- $T(n) \le T'(n)$ for all $n$ 。

$T'$ 不改變 algorithm，只是一個 proof device。

## $T'$ 的 recurrence

對於任何 $m \le n$ ，algorithm 的結構告訴我們

$$
T(m) \le T(\lfloor m/5 \rfloor) + T(s) + am
$$

其中 $s \le \lfloor 7m/10 \rfloor + c_1$ 是 partition 後實際 recurse 的 size。

現在用 $T'$ ：因為 $T \le T'$ pointwise，

$$
T(m) \le T'(\lfloor m/5 \rfloor) + T'(s) + am
$$

因為 $T'$ monotone 且 $s \le \lfloor 7m/10 \rfloor + c_1$ ，

$$
T'(s) \le T'(\lfloor 7m/10 \rfloor + c_1)
$$

再因為 $m \le n$ 所以 $\lfloor m/5 \rfloor \le \lfloor n/5 \rfloor$ ， $\lfloor 7m/10 \rfloor + c\_1 \le \lfloor 7n/10 \rfloor + c\_1$ ，monotone 再用一次：

$$
T(m) \le T'(\lfloor n/5 \rfloor) + T'(\lfloor 7n/10 \rfloor + c_1) + an
$$

右手邊不依賴 $m$ ，對所有 $m \le n$ 取 max：

$$
T'(n) \le T'(\lfloor n/5 \rfloor) + T'(\lfloor 7n/10 \rfloor + c_1) + an
$$

## Induction

要證 $T'(n) = O(n)$ ，即存在 $c > 0$ 和 $n\_0$ 使得 $T'(n) \le cn$ for all $n \ge n\_0$ 。

**Base case**：選 $n\_0$ 大到 recurrence valid，然後選 $c$ 大到 cover 所有 $n < n\_0$ 。

**Inductive step**：假設 $T'(k) \le ck$ for all $k < n$ （ $n \ge n_0$ ）。

$$
T'(n) \le c\lfloor n/5 \rfloor + c(\lfloor 7n/10 \rfloor + c_1) + an
$$

用 $\lfloor x \rfloor \le x$ ：

$$
T'(n) \le c\cdot\frac{n}{5} + c\left(\frac{7n}{10} + c_1\right) + an = \left(\frac{9c}{10} + a\right)n + c \cdot c_1
$$

選 $c$ 使得 $a \le \frac{c}{20}$ ，則 $\frac{9c}{10} + a \le \frac{19c}{20}$ 。

$$
T'(n) \le \frac{19c}{20}n + c \cdot c_1
$$

再選 $n\_0 \ge 20 c\_1$ ，使得 $c \cdot c\_1 \le \frac{c}{20}n$ ：

$$
T'(n) \le \frac{19c}{20}n + \frac{c}{20}n = cn
$$

Induction 完成。 $T'(n) = O(n)$ ，因為 $T(n) \le T'(n)$ ，所以 $T(n) = O(n)$ 。

## 回顧

三個 technical nuisances 各有各的 fix：

| 問題 | 處理方式 |
|------|---------|
| Rounding (floors/ceilings) | 塞進常數 $c_1$ |
| $O(n)$ 裡的 hidden constant | 寫成 explicit 的 $a$ |
| Base case threshold | 用 $n_0$ 和放大 $c$ 處理 |

## Takeaway

Median of medians 的證明有兩部分：

1. **Counting lemma**：pivot 保證較大邊最多 $(7/10)n + O(1)$ 。
2. **Recurrence induction**：這個 shrinkage 足以證明 linear time。

$T'$ 只是讓 recurrence 在 rounding 下安全。 $c\_1$ 和 $n\_0$ 不改變 theorem，只是讓 proof honest。
