---
title: "Zero Tolerance for Bias"
source: "https://app.notion.com/p/fadd4a8b768344f682c043d1d9027ba0"
---

[https://queue.acm.org/detail.cfm?id=3664645](https://queue.acm.org/detail.cfm?id=3664645)

## 為甚麼讀這篇論文？

有一位名為 Ryan 的網友在香港編程學會分享了它，我週末沒甚麼其它事就讀了。

## 論文寫甚麼？

論文研究的問題是 generate random permutation，然後重點講了三個常見的錯誤和它們的解決辦法。

## 錯誤一 ⋯ 太容易猜的 seed

```c++
srand(time(NULL));
```

是一個很糟糕的方法，只要大概知道程多運行（或將會運行）的時間，就不難找到一個 time range，也就是 seed range，假若那是彩票 ⋯ 就糟糕了。

## 解決方案一 ⋯ 使用更好的隨機源

論文中提及新的硬件往往有基於硬件的 true random source，例如 thermal noise，可以通過 `RDSEED` 指令獲得。

## 錯誤二 ⋯ 模 N 偏差

```c++
int sample = rand() % N;
```

在多數情況下，上面的代碼是錯的，它不會保證 sample 是 從 `0` 到 `N - 1` 平均分佈的。

`rand` 這個函數在 C library 裡面只保證給出一個由 0 到 `RAND_MAX` 中間平均分佈的值，而 `RAND_MAX` 本身只保證最少為 32767。

首先，假如 `N >= 32768` 就完了，比 `32767` 大的值的機率為 `0`。[^rand-range]

其次，假如 `RAND_MAX` 是 32767，而 `N` 是 10，那麼 `0` 出現的次數是 `3277` 次 `[0, 10, … 32760]` 都算是 `0` 。但 `9` 出現的次數只有 `3276` 次 `[9, 19, …, 32759]` 都算 `9`，但 `32769` 是不可能。

所以出 `8` 和 `9` 的機會，要比出 `0` 的機率少一點點，這個偏差其實不算大，而這個偏差會隨著 `N` 變大變的愈來愈誇張，例如 `N = 32767`﹐那麼 `0` 的機率是 `1` 的兩倍 ⋯

## 解決方案二 ⋯ rejection sampling

假設我們還是在解決 `N = 10` 的 case，我們可以拒絕 `rand()` 的 `output ≥ 32760`而重試。

得到任何一個數的機率將會是 $\frac{3276}{32767} + \frac{7}{32767}\frac{3276}{32767} + \left(\frac{7}{32767}\right)^2\frac{3276}{32767} + \cdots$ 。[^rejection-count]

簡化可得 $\frac{3276}{32767}\frac{1}{1 - \frac{7}{32767}} = \frac{1}{10}$ ，正是我們想要的結果。

## 錯誤三 ⋯ 不能從整個 array 中挑選第 k 位的值。

這段代碼也是錯的，而且這個錯誤最糟糕。

```c++
for (int i = 0; i < N; i++)
{
  swap(arr[i], arr[random(N)]);
}
```


原因是假設 `random(N)` 確實可以給你一個 uniformly random from `0` to `N-1`，這個算法也不能讓每一個 permutation 的 probability 都一樣。

這裡有一個簡單的實驗驗證，通過嘗試所有的可能性，我們可以準確的算到機率

```py
choices = []

for i in range(256):
    l = [(i // 64) % 4, (i // 16) % 4, (i // 4) % 4, (i // 1) % 4]
    choices.append(l)

hist = {}
for choice in choices:
    l = [str(x) for x in [1,2,3,4]]
    for i in range(4):
        l[i], l[choice[i]] = l[choice[i]], l[i]
    s = "".join(l)
    if s not in hist:
        hist[s] = 0
    hist[s] += 1
for k in hist:
    print(k, hist[k])
```


執行結果如下

```txt
2143 15
2341 14
2314 14
1342 14
3412 11
3241 11
3142 11
3124 11
2431 11
2413 11
1423 11
4321 10
4312 10
3421 10
2134 10
1324 10
1243 10
1234 10
4213 9
4132 9
3214 9
1432 9
4231 8
4123 8
```

這個明顯不是 uniform distribution，而且 `2143` 的 probability 機乎是 `4123` 的兩倍。

除了實驗驗證之外，我們也可以分析第一個數值的 `4` 的可能性。

其實只有兩種可能：

情況一，一開始 1 就和 4 swap，然後剩下三個都沒有和 1 swap，這樣的機率是 $\frac{1}{4}\left(\frac{3}{4}\right)^3$ ，或者

情況二，除了最後一次，誰也沒有和 4 swap，然後 4 和 1 swap，這樣的機率是 $\left(\frac{3}{4}\right)^3\frac{1}{4}$

除了這兩個情況外，其它是不可能了，所以總機率是 $\frac{27}{64}$ ，並不是 $\frac{1}{4}$ 。[^shuffle-probability]

不難用上述數據驗證分析正確。

## 解決方案三 ⋯ Fisher Yates shuffle

這個算法很有名，其實是個簡單單的變化而已。

```c
for (int i = 0; i < N; i++)
{
  swap(arr[i], arr[i + random(N - i)]);
}
```


這個算法確實可以做到每一個 permutation 的機率都一樣。其實就是再找第 k 個數值的時候，不考慮前面已經選好的。

假設原 `arr` 是 `arr[i] = i`，`arr[k] = m`的機率是

前面 $k - 1$ 次都沒挑到 $m$ ， $\frac{n-1}{n} \times \frac{n-2}{n-1} \times \cdots \times \frac{n-k}{n-k+1} = \frac{n-k}{n}$

然後挑到 m，就是 $\frac{1}{n-k}$

所以最終機率是 $\frac{n-k}{n} \times \frac{1}{n-k} = \frac{1}{n}$

由於 $k$ 和 $m$ 都是任意的，所以其實任何位置有任何數值的機率都一樣，所以每一個 permutation 的機率都一樣。[^uniform-permutations]

## 讀後感

其實除了有一定要求的情況下（比如彩票），每一個 permutation 的機率要完全一樣有那麼重要嗎？比如說我只是想要保證 quick sort 不糟糕所以預先 shuffle 一下，應該沒關係吧。

當是複習機率好了。

Cecilia,

8th, June, 2024

[^rand-range]: **勘誤：** 32767 只是 `RAND_MAX` 的最低要求，不能據此斷言所有 implementations 都無法產生更大的值。即使固定 `RAND_MAX = 32767` ，`N = 32768` 也剛好涵蓋所有可能 outputs，沒有這個 range 問題；無法涵蓋所有目標值的條件是 `N > RAND_MAX + 1` 。

[^rejection-count]: **勘誤：** 在 `RAND_MAX = 32767` 且 outputs 均勻的假設下，共有 32768 個可能 outputs。拒絕的 32760 到 32767 共 8 個，不是 7 個。因此每個目標值的機率應是 `(3276/32768) / (1 - 8/32768) = 3276/(32768 - 8) = 1/10`。Rejection sampling 的結論正確，但原文的計數和分母有誤。

[^shuffle-probability]: **勘誤：** 兩個 cases 各有機率 `(1/4) * (3/4)^3 = 27/256`，相加應是 `27/128`，不是 `27/64`。上面的 exhaustive histogram 中，首位是 4 的 permutations 的 counts 相加亦是 54，所以機率是 `54/256`。

[^uniform-permutations]: **證明缺口：** 各位置的 marginal distribution 均勻，不代表 permutations 的 joint distribution 均勻。反例是只從一個固定排列的 `n` 個 cyclic rotations 中均勻挑選：每個位置仍然均勻，但大部分 permutations 永不出現。Fisher–Yates 的正確證明是：假設每次 random choice 都獨立且均勻，每一個指定 permutation 的 successive conditional probabilities 相乘都是 `1/n * 1/(n-1) * ... * 1/1 = 1/n!`。
