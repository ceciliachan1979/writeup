# CodeForces contest 1541 (Div2) 第 B 題

問題係[呢度](https://codeforces.com/contest/1541/problem/B)

呢題都算考古，係 2021 年嘅一個比賽嘅一條題目。當年我只係 hea 玩，然後呢題係唔識，之後就冇乜點玩，直到最近先返碌。

一開始諗嘅係咁：

個 array 有成 100,000 個 elements，check all pairs 係冇可能。

有冇可能好似[two sum](https://leetcode.com/problems/two-sum)咁，for 每一個 $i$ ，count 到有幾多個 $j$ 呢？

個 key 問題就係呢條 equation $a_i a_j = i + j$ 係拆唔到左邊只有 $i$ ，右邊只有 $j$ 。

之後就 stuck 咗，諗咗一啲行唔通嘅路，例如 $i+j$ 只能由 $3$ 去到 $2n-1$，有冇可能係 factor 晒呢啲 numbers 咁。

最後係問咗人，睇答案。

> 承認自己做唔到，放棄，都係一件重要嘅事。唔肯放棄，又做唔到，只係會浪費時間，其實幫唔到啲咩。

個 key idea 係條 equation 個左手邊係乘數，乘數 grow 得好快的。

Fix 咗 $a_i$，估 $a_j$ 嘅值係 $k$，就知道 $j=a_i k - i$，如果 $j$ 太大，就玩完冇可能，否則檢查吓 $a_j$ 係咪等於 $k$ 就得。

呢個 idea 其實我諗過，但自己否決咗，做呢條嘅時候有啲鬼揞眼，冇留意到 array entries 係 distinct 呢個重要嘅 constraint，佢已經 bold 咗我都睇唔到 |||

假如冇呢個 constraint，array entries 全部係 1，咁每一個 entries 都要 loop $O(n)$ 次試，咁係會失敗。

但如果係 distinct，個 number of multiples 就有一個好靚嘅 bound

假設我哋 `b = sorted(a)`

- $b_1 \ge 1$，有最多 $2n-1$ 個 multiples $\le 2n-1$
- $b_2 \ge 2$，有最多 $\left\lfloor \frac{2n-1}{2}\right\rfloor$ 個 multiples $\le 2n-1$
- $b_3 \ge 3$，有最多 $\left\lfloor \frac{2n-1}{3}\right\rfloor$ 個 multiples $\le 2n-1$

所以 algorithm 總共檢查最多 $(2n+1)(1 + \frac{1}{2} + \frac{1}{3} + \cdots + \frac{1}{n}) = O(n\log n)$ 個 multiples。

https://stackoverflow.com/questions/25905118/finding-big-o-of-the-harmonic-series