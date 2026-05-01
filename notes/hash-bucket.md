# Hash Table 最大 Bucket Size

把 $n$ 個 key 用 random hash function 放進 $n$ 個 bucket，最大的 bucket 有多大？答案是 $\Theta\!\left(\dfrac{\log n}{\log \log n}\right)$ ，with high probability。這個 result 是 hash table 分析裏面很經典的一個，我每次都記不住怎麼推，寫一下。

## Upper Bound

固定一個 bucket，第 $i$ 個 key 落進這個 bucket 的 indicator 設為 $X\_i \sim \text{Bernoulli}\!\left(\frac{1}{n}\right)$ ，各個 key 的 hash 是 independent 的，所以 $X\_1, X\_2, \ldots, X\_n$ 是 independent。這個 bucket 的 size 就是

$$
X = \sum_{i=1}^{n} X_i
$$

期望值 $\mu = \mathbb{E}[X] = n \cdot \frac{1}{n} = 1$ 。

### Chernoff Bound

對於任何 $t > 0$ ，

$$
\Pr[X \ge k] = \Pr\!\left[e^{tX} \ge e^{tk}\right] \le \frac{\mathbb{E}\!\left[e^{tX}\right]}{e^{tk}}
$$

最後一步是 Markov's inequality。因為 $X_i$ 是 independent，MGF 可以拆開：

$$
\mathbb{E}\!\left[e^{tX}\right] = \mathbb{E}\!\left[e^{t\sum X_i}\right] = \prod_{i=1}^{n} \mathbb{E}\!\left[e^{tX_i}\right]
$$

每一個 Bernoulli 的 MGF，就是按定義展開 expectation。 $X_i$ 只有兩個值： $0$ （概率 $1 - \frac{1}{n}$ ）和 $1$ （概率 $\frac{1}{n}$ ），所以

$$
\mathbb{E}\!\left[e^{tX_i}\right] = e^{t \cdot 0} \cdot \left(1 - \frac{1}{n}\right) + e^{t \cdot 1} \cdot \frac{1}{n} = 1 - \frac{1}{n} + \frac{e^t}{n} = 1 + \frac{e^t - 1}{n}
$$

所以乘起來：

$$
\mathbb{E}\!\left[e^{tX}\right] = \left(1 + \frac{e^t - 1}{n}\right)^n
$$

用 $1 + x \le e^x$ ：

$$
\left(1 + \frac{e^t - 1}{n}\right)^n \le \left(e^{\frac{e^t - 1}{n}}\right)^n = e^{e^t - 1}
$$

（這裏 $\mu = 1$ 所以 exponent 就是 $\mu(e^t - 1) = e^t - 1$ 。）

代回去：

$$
\Pr[X \ge k] \le \frac{e^{e^t - 1}}{e^{tk}}
$$

對 $t$ 取最優值。令 $\frac{d}{dt}\left[e^t - 1 - tk\right] = 0$ ，得 $e^t = k$ ，即 $t = \ln k$ 。代入：

$$
\Pr[X \ge k] \le \frac{e^{k - 1}}{k^k} = \frac{e^{k-1}}{k^k}
$$

用 Stirling $k! \ge (k/e)^k$ 的話，這個就是 $\le \dfrac{e^{k-1}}{k^k} < \dfrac{e^k}{k^k} = \left(\dfrac{e}{k}\right)^k$ 。

所以 Chernoff bound 給了我們：

$$
\boxed{\Pr[X \ge k] \le \left(\frac{e}{k}\right)^k}
$$

（其實我們是直接從 MGF 推出來的，Stirling 只是用來幫助理解這個 bound 的形狀。）

### Union Bound

以上是對一個固定 bucket 的 bound。我們有 $n$ 個 bucket，設第 $j$ 個 bucket 的 size 為 $X^{(j)}$ ，我們關心的是

$$
\Pr\!\left[\max_j X^{(j)} \ge k\right]
$$

Union bound 說：

$$
\Pr\!\left[\max_j X^{(j)} \ge k\right] = \Pr\!\left[\bigcup_{j=1}^{n} \left\lbrace X^{(j)} \ge k\right\rbrace\right] \le \sum_{j=1}^{n} \Pr\!\left[X^{(j)} \ge k\right] \le n \cdot \left(\frac{e}{k}\right)^k
$$

所以只要找到 $k$ 使得 $n \cdot \left(\frac{e}{k}\right)^k \le \frac{1}{n}$ ，即 $\left(\frac{e}{k}\right)^k \le \frac{1}{n^2}$ ，就能保證 with high probability 最大 bucket size 不超過 $k$ 。

這裏 $\frac{1}{n}$ 不是甚麼 magic number。在 randomized algorithms 的文獻中，"with high probability" (w.h.p.) 的標準定義是：對於任意常數 $c > 0$ ，事件以概率 $\ge 1 - \frac{1}{n^c}$ 成立。我們這裏選了 $c = 1$ （即失敗概率 $\le \frac{1}{n}$ ），所以 union bound 那一步需要每個 bucket 的失敗概率 $\le \frac{1}{n^2}$ 。如果你想要更強的 $\frac{1}{n^c}$ ，只要把 $\frac{1}{n^2}$ 換成 $\frac{1}{n^{c+1}}$ 就好了，最終 $k$ 只是差一個常數 factor，asymptotic order 不變。

### 為甚麼是 $\dfrac{\ln n}{\ln \ln n}$ ？

目標是找 $k$ 使得 $\left(\frac{e}{k}\right)^k \le \frac{1}{n^2}$ 。取 log：

$$
k \ln k - k \ge 2 \ln n
$$

左邊是 $k \ln k$ 主導（因為 $k$ 前面的係數只是 $-1$ ，遠小於 $\ln k$ ），所以大約需要

$$
k \ln k \approx \ln n
$$

這是一個 $k \ln k = C$ 的方程，怎麼解？先試 $k = \ln n$ ：

$$
\ln n \cdot \ln \ln n
$$

這比 $\ln n$ 大太多了（多了一個 $\ln \ln n$ 的 factor）。那我們除回去試 $k = \dfrac{\ln n}{\ln \ln n}$ ：

$$
\frac{\ln n}{\ln \ln n} \cdot \ln\!\left(\frac{\ln n}{\ln \ln n}\right) = \frac{\ln n}{\ln \ln n} \cdot \left(\ln \ln n - \ln \ln \ln n\right) \approx \ln n \cdot \left(1 - \frac{\ln \ln \ln n}{\ln \ln n}\right)
$$

當 $n$ 大的時候 $\frac{\ln \ln \ln n}{\ln \ln n} \to 0$ ，所以 $k \ln k \approx \ln n$ ，剛剛好！

這就是 $\dfrac{\ln n}{\ln \ln n}$ 的來源——它是 $k \ln k = \ln n$ 的 asymptotic solution。

### 完成 Upper Bound

設 $k = c \cdot \dfrac{\ln n}{\ln \ln n}$ ，代入 $k\ln(k/e) \ge 2\ln n$ ：

$$
c \cdot \frac{\ln n}{\ln \ln n} \cdot \left(\ln c + \ln \ln n - \ln \ln \ln n - 1\right) \ge 2 \ln n
$$

當 $n$ 夠大，括號裏 $\sim \ln \ln n$ ，所以左邊 $\sim c \cdot \ln n$ 。只要 $c > 2$ 就足夠了。取 $c = 3$ ：

$$
\Pr[X \ge k] \le \left(\frac{e}{k}\right)^k < \frac{1}{n^2}
$$

Union bound 所有 $n$ 個 bucket：

$$
\Pr\!\left[\max \text{ bucket size} \ge k\right] \le n \cdot \frac{1}{n^2} = \frac{1}{n}
$$

這就證明了：當 $n$ 足夠大時，

$$
\Pr\!\left[\max \text{ bucket size} \ge 3 \cdot \frac{\ln n}{\ln \ln n}\right] \le \frac{1}{n}
$$

## 實驗

理論歸理論，跑一下看看。對 $n = 10, 100, 1000$ ，每個跑 100 次實驗，記錄最大 bucket size。

```python
import random, math

random.seed(42)

for n in [10, 100, 1000]:
    max_buckets = []
    for _ in range(100):
        buckets = [0] * n
        for _ in range(n):
            buckets[random.randint(0, n - 1)] += 1
        max_buckets.append(max(buckets))
    avg_max = sum(max_buckets) / len(max_buckets)
    bound = 3 * math.log(n) / math.log(math.log(n))
    print(f"n={n:>5}: avg_max={avg_max:.2f}, "
          f"min={min(max_buckets)}, max={max(max_buckets)}, "
          f"bound={bound:.2f}")
```

結果：

| $n$ | 平均最大 bucket | 實驗中最小 | 實驗中最大 | $3\dfrac{\ln n}{\ln \ln n}$ |
|-----|----------------|-----------|-----------|---------------------------|
| 10 | 2.70 | 2 | 4 | 8.28 |
| 100 | 4.19 | 3 | 7 | 9.05 |
| 1000 | 5.47 | 5 | 7 | 10.72 |

Bound 比實際值大不少，這是預期的——我們用了 union bound（本身就鬆）再加上 Chernoff bound 也不是 tight 的。但重點是 100 次實驗中沒有一次超過 bound，這和 w.h.p. 的保證一致。

## 參考
- Mitzenmacher & Upfal, *Probability and Computing*, Chapter 5
- [Wikipedia: Balls into bins problem](https://en.wikipedia.org/wiki/Balls_into_bins_problem)
