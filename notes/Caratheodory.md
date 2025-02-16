# Carathéodory’s theorem

這是 Farkas lemma 系列的第兩篇，今天我們會講一個叫做 Carathéodory’s theorem 的 theorem，這個 theorem 的 statement 是這樣的：

> Let $X = x_1, x_2, \ldots, x_n$ be points in $\mathbb{R}^d$ , for any point $x \in \text{ConvexHull}(X)$ , there exists subset $I \subseteq \{1, 2, \ldots, n\}$ such that $Y = \{x_j : j \in I\}$ is affine independent and $x \in \text{ConvexHull}(Y)$ .

我們之後需要的，是它的一個 conic variant：

> Let $X = x_1, x_2, \ldots, x_n$ be points in $\mathbb{R}^d$ , for any point $x \in \text{ConicHull}(X)$ , there exists subset $I \subseteq \{1, 2, \ldots, n\}$ such that $Y = \{x_j : j \in I\}$ is linearly independent and $x \in \text{ConicHull}(Y)$ .

## 定義

假設我們有一些 vectors $\{x_0, x_1, \cdots x_k\}$ 

- 如果 $\sum c_i x_i = 0 \implies c_i = 0$ ，那麼這些 vectors 是 linearly independent 的。
- 如果 $\sum_{i\ne 0} c_i (x_i - x_0) = 0 \implies c_i = 0$ ，那麼這些 vectors 是 affine independent 的。當然 $x_0$ 是可以任意選的。

另外
- 如果 $c_i \ge 0$ ，那麼 $\sum c_i x_i$ 叫做一個 conic combination。
- 如果 $c_i \ge 0$ 且 $\sum c_i = 1$ ，那麼 $\sum c_i x_i$ 叫做一個 convex combination。

## Intuition

Intuition 就是想法，它並不嚴謹，主要是幫助我們理解定義。

- linear independent 是指這些 vectors 裡面每一個都會擴大 span。
- affine 是指我們沒有 origin，假設你只有一個 point，其實你不能通過這個 point 去做出一個 vector，只有當我們有兩個 points 的時候，才能有 vector，也只是在這條線上，要 span 出一個平面，就需要三個 points。linear combination of points 指的不是從 origin scale 後加，而是在這些點做找 midpoint 之類的操作。
- affine independent 是指這些 points 每一個都會擴大可以 affine 構造的 space。

- convex 是指如果你有兩個 points，那麼這兩個 points 之間的所有點都包括了，就是說，沒有洞或者彎可以擋住任何兩點，就像一個鼓起來的包子。
- 一個線段 $PQ$ 中間的任何點，就是 $aP + bQ$ ， $a, b \ge 0$ ， $a + b = 1$ 。所以 convex combination 其實就是包裡面的點。多於一個點的時候，其實可以兩個兩個的做 convex combination，例如任何一個三角形的點，都可以先連去一個 vertex，這條線自然會把另外一條邊有交點，那麼我們先用兩點的 convex combination 一次找到交點，我們再用這個交點和 vertex 做 convex combination 就可以了。

- cone 是尖的，最簡單的 cone 是 first quadrant，generalize 可以看成一個 cone 的每一個 vector，都可以無限 scale 向一個方向，然後 scale 之後依然是在 cone 裡面。然後我們希望 cone 是 convex 的，所以它們的 convex combination 都包了。

- conic combination 指的就是 scale 向同一個方向後做 convex combination。比如說 $aP + bQ + cR$ ，其實可以看成 $\frac{1}{3}(3aP) + \frac{1}{3}(3bQ) + \frac{1}{3}(3cR)$ ，這樣就可以看出 convex combination of scaled vector 了。

- 各種 Hull 就是大包圍，所有有可能的點都要了。

有了上述的理解，就可以去理解 theorem 的 statement 了。

- 它的 convex 版基本上就是說如果有一個點在 convex hull 裡，先說是在平面上吧，那麼它必然可以找到一個三角形去包括它，不用所有的點。

- 它的 conic 版基本上就是說如果你用一堆 vector 去 span 一個 conic hull，其實已經在 span 裡的是沒有需要的，只要一個 linear independent 的 subset 就可以了。

## 證明 Conic 版

我們先證明 conic 版，先把原文寫得更 algebraic 一些，方便操作。

假如我有一些 vectors $a_1, a_2 \cdots a_n$ ，它們的 conic hull 可以寫成 $\{Ax:x \ge 0\}$ ，不難看出這只是把 conic combination 寫成 matrix 格式而已。

> 留意 $x$ 是一個 vector， $x \ge 0$ 指的是所有 component 都 $\ge 0$ 。

如果 $x$ 的某個 component 是 $0$ ，那麼我們可以把這個 dimension 和 $A$ 的對應的 column vector 刪掉，這樣並不會影響 $Ax$ 的值，也不會影響 $x \ge 0$ 這個事實。當然，你不能把所有 column 都刪掉 ⋯

接下來，我們的目標就是一直刪 $A$ 的 column，直到剩下的 column 是 linearly independent 為止。

假設我們做得到，我們就 prove 了 conic 版的 theorem 了，因為我們找到了一個 linearly independent 的 subset 可以使 $Ax$ 在它們的 conic hull 裡。

首先我們排除一些 trivial 的 case，比如 $x = 0$ ，明顯 $0$ 在隨便一個 vector 的 conic hull 裡。

另外假如 $A$ 的 column 本身就 linearly independent，那麼我們也不需要做甚麼了，取所有 vectors 做這個 subset 就可以了。

假設 $A$ 的 column 是 linearly dependent 的，就會存在一個 $v$ 使得 $Av = 0$ ， $v \ne 0$ 。假設這個 $v$ 的所有 component 都是正數，我們可以取 $v=-v$ ，這樣可以使 $v$ 必然有一個負數的 coordinate。

接下來，我們先設 $\lambda = 0$ ，考慮 $x + \lambda v$ ，無論 $\lambda$ 是甚麼， $A(x + \lambda v) = Ax + \lambda Av = Ax$ ，所以我們可以慢慢的加 $\lambda$ ，因為 $v$ 有負數 coordinate，總會有一個足夠大的 $\lambda$ 會使 $x + \lambda v$ 會出現一個 $0$ ，這個時候，我們就可以刪了對應的 column 了。

接下來，我們就 repeat 上面的步驟，直到 $A$ 的 column 是 linearly independent 為止。因為我們已經排除了 $Ax = 0$ 這個 case，所以這個過程一定不會刪掉所有 column，而停下時，剩下的 column 一定是 linearly independent 的。

這樣就證了 conic 版的 theorem。

## 證明 Convex 版

雖然我們用不著，但可以順便證明 convex 版的 theorem。

Convex 版的世界沒有 origin，所以我們可以選其中一點 $B$ 用來做 origin。

假設 $x$ 是 convex hull 的一個 point， $A$ 可以寫成

$$
\begin{align*}
x &= \sum_{i=1}^n \alpha_i a_i \\
&= \sum_{i=1}^n \alpha_i (a_i - B) + \sum_{i=1}^n \alpha_i B \\
&= \sum_{i=1}^n \alpha_i (a_i - B) + B \\
x - B &= \sum_{i=1}^n \alpha_i (a_i - B)
\end{align*}
$$

這樣就可以把 convex 版的 theorem 轉化成 conic 版的 theorem 了，找到一個 linear independent 的 subset $b_i$ ，使 $x - B$ 在它們的 conic hull 裡。

$$
\begin{align*}
x - B &= \sum_{i=1}^d \beta_i (b_i - B) \\
x &= \sum_{i=1}^d \beta_i b_i + \left(1 - \sum_{i=1}^d \beta_i\right) B
\end{align*}
$$

這幾乎就是 convex 版的 theorem 了，唯一的問題是 $1 - \sum_{i=1}^d \beta_i$ 是不是 $\ge 0$ 。

記得我們理解 convex 版是 $x$ 在某個三角形裡，假如我們挑了一個 $B$ 沒有任何一個包括 $B$ 的三角形能包括 $x$ ，那就必然會有負值的問題，關鍵是我們怎麼挑 $B$ 。

合理的選法是找最接近 $x$ 的 $B$ ，但這樣可以保證沒有負值嗎？

另外一個想法就是假如某個 $B$ 不成，我們可以把它刪掉嗎？利用找到了的負值公式，可以說明 $x$ 在沒有 $B$ 的 convex hull 裡嗎？

可以說明總有一個 $B$ 可以嗎？

## 升 dimension trick

看了這個 reference，原來我們可以用升 dimension trick 來證明 convex 版的 theorem。

https://en.wikipedia.org/wiki/Carath%C3%A9odory%27s_theorem_(convex_hull)

我們首先把所有 vectors 都升一個 dimension，最後 dimension 的值是 1。

現在 $x$ 還是一個 convex combination，當然也是一個 conic combination。

用 conic 版，可以證出 $x$ 在一個 linearly independent 的 subset 的 conic hull 裡。

重點來了， $x$ 的最後一個 coordinate 是 1，所以這個 conic combination 其實也是一個 convex combination。

然後因為 linear independent，所以刪掉最後一個 dimension 的話它其實是 affine independent 的。考慮 $\sum c_i (v_i - B) = 0 \implies \sum c_i v_i - \sum c_i B = 0$ ，這個時候如果我全部左手邊 vector 加回一個 dimension，那麼這個 sum 都最後一個 dimension 還是 $0$ ，所以可以利用 linear independency claim $c_i = 0$ ，這就證了 affine independent。

這樣就證了 convex 版的 theorem。
