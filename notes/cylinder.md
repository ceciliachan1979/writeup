# Volume of water in inclined cylinder

假如有一個 cylinder，它的半徑是 R，現在它的底部在 $(0, 0, 0)$ ，而它和 x-axis 平行。這時候沿著 z-axis 逆時針轉動 $\alpha$ 度 ( $0 < \alpha < 90$ )，y-axis 指向天空。

往 cylinder 裡面倒水，水的高度剛好讓 cylinder 的底部全濕了，這時候水的體積如何計算？

## 新的座標系

首先，我們希望有一個新的座標系，讓 cylinder 的底部在 $(0, 0, 0)$ ，而 cylinder 還是指向和新的 x-axis 平行，而 z-axis 不變。

首先，我們算一下水平面的 unit normal，在原本的座標系是 $\vec{j}$ ，在新的座標系就是 $\vec{n} = \sin\alpha \vec{i} + \cos\alpha \vec{j}$ 。所以已知水平面公式是 $n \cdot \vec{p} = C$ ，代入 $p = (0, R, 0)$ 就可以得到水平面的方程式在新的座標系是 $x\sin\alpha + y\cos\alpha = R\cos\alpha$ 。

## Integral

現在我們可以用 integral 來計算水的體積了。計劃是在新的 座標系裡面，對於 cylinder 的底部為 domain 做一個 double integral，integrand 就是水的高度 $x = \frac{R\cos\alpha - y\cos\alpha}{\sin\alpha} = (R-y)\cot\alpha$ 。

$$
\begin{align}
& \iint_{B} (R-y)\cot\alpha dy dz \\
=& \iint_{B} (R - r\cos\theta)\cot\alpha r dr d\theta \\
=& \int_{0}^{R} \int_{0}^{2\pi} (R - r\cos\theta)\cot\alpha r d\theta dr  \\
=& \int_{0}^{R} 2\pi Rr \cot\alpha dr \\
=& \pi R^3 \cot\alpha
\end{align}
$$

其中第第二行使用的是 Jacobian 變換，第三到第四行是發現 integrate $\cos\theta$ 的結果是 0，其它都是簡單的操作。

這個答案可以想像它其實就是一個打直的 cylinder 而高度是 $R\cot{\alpha}$ ，也許能找到一個幾何的解釋。
