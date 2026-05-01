# norm bound

下面一連兩三篇都會寫 Farkas lemma 的證明，這一集我會先寫一個簡單的 result，考慮一個 convergent sequence $x\_i \in \mathbb{R}^n$ ，經過一個 linear transformation 之後， $Ax\_i \in \mathbb{R}^m$ 會 converge 嗎？

# Proof
回憶一下 Cauchy sequence 的定義，對於任意的 $\epsilon > 0$ ，存在 $N$ 使得當 $i, j > N$ 的時候， $\|x\_i - x\_j\| < \epsilon$ 。

因為 $x\_i$ converge，所以 $x\_i$ 是 Cauchy，對於任意的 $\epsilon > 0$ ，存在 $N$ 使得當 $i, j > N$ 的時候， $\|x\_i - x\_j\| < \frac{\epsilon}{\|A\|}$ 。

所以 $\|Ax\_i - Ax\_j\| = \|A(x\_i - x\_j)\| \le \|A\| \cdot \|x\_i - x\_j\| < \frac{\epsilon}{\|A\|} \cdot ||A|| = \epsilon$ ，所以 $Ax\_i$ 也是 Cauchy， $Ax\_i$ converge。

# 細節
 $\|A\|$ 是什麼呢？ $\|A\|$ 是 operator norm，定義為 $\|A\| = \max_{\|x\| = 1} \|Ax\|$ 。

假如 operator norm 存在，那就不難理解為甚麼 $\|A(x\_i - x\_j)\| \le \|A\| \cdot \|x\_i - x\_j\|$ 了，只要考慮把 $x\_i - x\_j$ 寫成一個 scaled unit vector 就後，這個 inequality 就顯然了。

# 那 operator norm 為甚麼存在呢？
 $Ax$ 可以寫成 $Ax = \sum\_{i=1}^n x\_i A\_i$ ， $A\_i$ 是 $A$ 的第 $i$ 個 column。使用 triangle inequality，我可以寫成 $\|Ax\| = \|\sum\_{i=1}^n x\_i A\_i\| \le \sum\_{i=1}^n |x\_i| \cdot \|A\_i\|$ 。

當 $\|x\| = 1$ 的時候，自然 $|x\_i| \le 1$ ，所以 $\|Ax\| \le \sum\limits\_{i=1}^n \|A\_i\|$ 。

# Generalize
假設我們有一個 bounded operator $A$ ， $x\_i$ converge， $Ax\_i$ converge 嗎？答案是肯定的，因為 bounded operator 的定義就是 $\|Ax\| \le \|A\| \cdot \|x\|$ 。我們其實並不特別在意 $A$ 是 linear transformation， $A$ 只要是 bounded operator 就可以了。

其實 "A linear operator between normed spaces is bounded if and only if it is continuous."，如果你無條件相信 $A$ 是 continuous 的話，那麼 $Ax_i$ converge 也是顯然的。

# 參考
- https://en.wikipedia.org/wiki/Bounded_operator
