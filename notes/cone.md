# Cone

這是 Farkas lemma 系列的第三篇，今天我們會證一個看似看簡單的問題：

Cone is closed

它只是看似簡單，實際上它的證明是是要用到前兩篇的。

## 定義

其實上一篇已經寫過，一個 cone 是這個 set $\{Ax: x \ge 0\}$ ，我們想要證明這個 set 是一個 closed set。

## 錯誤的嘗試 1，利用 continuity

我們可以用 continuity 來證明這個問題，因為 $Ax$ 是一個 linear function，所以它是 continuous 的。然後明顯 $x \ge 0$ 是一個 closed set，所以 $Ax$ 是一個 closed set。

錯！

 $\{(x, y) : xy = 1\}$ 也是一個 closed set，但它的 projection 在 $x$ 和 $y$ 上是 open set。

## 錯誤的嘗試 2，利用 boundedness

如果我們有一個 sequence in $Ax$ ，我們可以用 boundedness 來證明這個問題。接近 limit 的點它們的 distance 是可以弄的 arbitrarily small，它們的 preimage 可不可以也弄成 arbitrarily small 呢？

不能 ⋯ 最簡單的 projection 都不成。 $(x,y,z) \to (x,y)$ 這個 map 可以把一個 convergent 的 sequence 弄出一個 arbitrary $z$ 的 preimage，沒有辦法 claim 它的 preimage 是 Cauchy。

> 假如我們被 project 掉的 dimension 是 compact 的話，這個 claim 就成立了。

詳見：https://math.stackexchange.com/questions/22697/projection-map-being-a-closed-map

## 正確的證明

在上一篇，我們證明了一個 cone 裡面的其中一個點，可以用一個 linearly independent 的 subset 來表示。

假如我們拿著 $A$ 的 column，找出所有 linearly independent 的 subsets，任何一個這個 cone 裡面的點，都可以用最少一個 subset 來表示。換句話說， $Ax$ 是這些 subsets 的 union。

我們只有 finite 個 column，所以這些 subsets 的 union 是 finite 個的 union，我只要證明其中一個是 closed set，就可以證明整個 cone 是 closed set，因為它是 finite 個 closed sets 的 union。

最後一步，我們假設 $A$ 的 columns 是 linearly independent 後，要證明 $\{Ax: x \ge 0\}$ 是 closed set。

 $A$ 的 column 是 linearly independent 的最大好處，就是對於一個固定的 $y$ ， $Ax = y$ 只有一個符合的 $x$ 。換句話說， $Ax$ 是一個 injective 的 mapping。當然，把 codomain 定義為 $\{Ax\}$ 的 話這個 mapping 就是 bijective 的。

因為 $A$ 不一定是 square matrix，我們不能用 $A^{-1}$ ，但是我們可以用 pseudo inverse。

 $A$ 的 pseudo inverse 是 $A^+ = (A^TA)^{-1}A^T$ ，明顯 $A^+Ax = (A^TA)^{-1}A^TAx = x$ ，所以我們找到了一個 inverse mapping。

> 因為 $x^TA^TAx ＝(Ax)^T(Ax) = 0 \iff x = 0$ ，所以 $A^TA$ 是 positive definite 和 inverible。在非 full column rank 的情況下，這個 formula 並不適用，而是要用 singular value decomposition。

詳見：https://en.wikipedia.org/wiki/Moore%E2%80%93Penrose_inverse

用第一篇的證明，我們就可以知道 $A^+$ 這個 mapping 會 map 一個 convergent sequence 到一個 convergent sequence。換句話說，在 $\{Ax: x \ge 0\}$ 裡面的一個 limit point 會有一個 convergent sequence，這個 sequence 的 pre-image 只是一個 convergent sequence。它的 limit 當然也符合 $x \ge 0$ ，所以 $\{Ax: x \ge 0\}$ 會包含它的 limit point，換句話說， $\{Ax: x \ge 0\}$ 是 closed set。

這個用 Carathéodory’s theorem 的證明來自這篇問答：https://math.stackexchange.com/questions/1831401/how-do-you-prove-that-ax-mid-x-geq-0-is-closed

## Generalize

Continuous function 的一個 property (或者說是 definition) 就是 open set 的 preimage 是 open set，而當 continuous function 同時有 continuous inverse 的時候，open set 的 image 也是 open set，那麼我們就可以 claim closed set 的 image 也是 closed set 了，無非就是 complement 而已 ⋯

像這樣有一個 continuous inverse 的 mapping，我們叫它 homeomorphism。所以其實我們證明了 homeomorphism 可以把 closed set map to closed set。

假如兩個 spaces 之間有一個 homeomorphism，就 topology 來說它們沒有分別，就像咖啡杯和甜甜圈一樣 ⋯

> 詳見：https://en.wikipedia.org/wiki/Homeomorphism

連結有甜甜圈動畫 ⋯
