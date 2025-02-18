# Linear programming

上一篇講到 linear programming，自然可以複習一下 linear programming，我們會講這三點：

- geometry/algebra 對應
- Simplex algorithm 和它的 data structure tableau
- 如何在 tableaux 中找到 dual 的理解

我們首先假設 standard form

$$\begin{align*}
\text{min}\quad z =& c^Tx\\
\text{subject to}\quad Ax =& b\\
\quad x \ge& 0
\end{align*}$$

 $Ax = b$ 可以讓我們很方便的把這些 constraint 做 row operations，做這些 row operations 不會對 feasible solution 有任何影響。

## Geometry/algebra 對應
我們先研究一下 objective，先暫時假設 $w$ 和 $z$ 是固定，所有符合 $w^Tx = z$ 的 $x$ 就是一個 hyperplane。

如果我們 optimize $\|x\|$ 就會發現 $|z|$ 其實就是這個 hyperplane 上 $\|x\|$ 最少的值。

換句話說， $|z|$ 是這個 hyperplane 和 origin 的距離。

在我們的 LP 裡， $z$ 不是固定的，換句話說，我們在考慮一系列平行的 hyperplane。其實這個 program 要做的，就是在 feasible region 中找一個點，使得這個點上的 hyperplane 和 origin 最接近。

> 又或者當 $z < 0$ 時和 origin 愈遠愈好

接下來我們看 feasible region。 $Ax=b$ 可以看成有 $m$ 個 $A$ 的 row 和 $x$ 做 dot product 後等於 $b$ 裡對應的 entry，換句話說，是 $m$ 個 hyperplane 的 intersection。

當然還有 $x \ge 0$ ，說明它只在 1st quadrant。

綜合起來，想像一個 hyperplane 在平移，和代表著其它 constraints 的 hyperplane 做 intersection，最終的 intersection 裡面有 $x$ ，然後希望能滑到一個地方和 origin 最接近(或最遠）。

所以如果 constraint 不在一個「角」上，大概應該還能繼續滑 ⋯

Rigorous 一點，如果存在 $p$ such that $A(x+p) = A(x-p) = b$ , $x+p\ge 0$ , $x-p\ge 0$ ，那麼 $w^T(x \pm p) = w^Tx \pm w^Tp$ 。假設 $w^Tp \ne 0$ ，那麼總有一個方向會 reduce $z$ ，這個點就不是 optimal。

如果 $w^Tp = 0$ 呢？這時你總可以取最大或最少 $\|p\|$ 令 $x+p$ 或 $x-p$ 有等於 0 而不能繼續變大，這時候你又回到「角」上去了。

所以 geometry notion 是答案在角上。

為了方便使用，我們看看「角」對應的時有甚麼 algebraic property？「角」對應的是 basic solution。basic 的意思是指 solution 上非零的值，對應 $A$ 的 column，是一個 basis (i.e. linearly independent set)。

假如 $x$ 非 basic，那麼存在 $Ap = 0$ ， $p$ 只有在 $x$ 非零的位置上非零。

> 記得 $Ap$ 可以看成用 $p$ 做 $A$ 的 column 的 linear combination

這樣就做成 $A(x+p) = A(x-p) = b$ 了，然後因為 $p$ 只有在 $x$ 非零的位置上非零，你總是可以找到足夠小的 $p$ 去 maintain feasibility 的。

所以非 basic solution 不是「角」。同理也可證如果不是「角」，那麼 $p$ 存在， $Ap = 0$ ，而且 $p$ 明顯不可能在 $x$ 的值為 $0$ 的地方非零(因為 $x-p$ 或者 $x+p$ 將會 infeasible)，所以不是 basic solution。

因為答案只能是「角」，我們只需要看 basic solution 就好了。

在 feasible region 的點有無數個，但 basis 有限，一旦定下了用哪些 column 做 basis，其實 $Ax=b$ 就只有一個答案，所以我們把無限個可能性換成有限個答案了。

## Simplex
假設我們要 solve 這個 linear program：

$$\begin{align*}
\text{min}\quad z =& c^Tx \\
\text{subject to}\quad Ax =& b \\
x\ge& 0
\end{align*}$$

我們先把它變成

$$\begin{align*}
\text{min}\quad y \\
\text{subject to}\quad Ax + a =& b \\
-z + c^Tx =& 0 \\
-y + 1^Ta =& 0\\
x\ge& 0
\end{align*}$$

 $a$ 是 artificial variables，我們的首要目標是要把 $a$ 做成 $0$ ，把它做成 $0$ 就能把它變回原來的模樣了。

把它改成這樣的原因是因為我們有一個免費的 basic feasible solution $x=0,a=b$ 。接下來，我們只要想辦法換 basis，令到 objective 愈來愈好就可以了。

由於我把 $-y+1^Ta ＝0$ 寫成是一個 constraint，那麼顯然它可以加上其它 constraint 而不改變它的本質，我可以寫

$$\begin{align*}
Ax + a &= b \\
1^T(Ax + a) &= 1^Tb \\
1^TAx + 1^Ta &= 1^Tb \\
-y +1^Ta - (1^TAx + 1^Ta) &= -1^T b\\
-y - 1^TAx &= -1^T b
\end{align*}$$

**TODO**: 用 Tableaux 講

> $1^T A$ 其實就是把 $A$ 想成 column vector of rows，所以是 sum of all rows。

最後一步寫成了 reduced cost 的模樣。In general $y$ ，objective，寫成非 basic variable 的 linear combination。

這個 reduced cost form 有甚麼好處呢？它是用來 evaluate 哪一個 non-basic variable (它們現在的值是 0) 可以增加它去 improve objective。當然，負值的 reduced cost 可以減少 objective。然後雖然 basic variable 需要調整，但它們在 reduced cost form 的 coefficient 是 0，不影響 objective value 的。

當然，non-basic variable 的調整可能是有限制的，當 non-basic variable increase 的時候，有些 basic variable 可能需要 decrease，然後一旦有任何 basic variable 到 0 了，就不能繼續 increase non-basic variable 了。

這個時候有一個新的 non-basic variable 變成非 0 (enter the basis)，然後有另一個 basic variable 變成 0 (leave the basis) 了。

一開始的時候，當 non-basic variable 要進入 basis 的時候，basic variable 需要的變化其實是很簡單的。因為 $a$ 的 coefficient 都是 $1$ ，non-basic variable 令到某行 constraint 的總值漲多少，用 $a$ 扣回去就可以保持 constraint 的值不變了。

我們希望接下來每一步都可以如此簡單的 update basis，換句話說，我希望 basis column 永遠是一個 identity matrix。要做到這一點其實不難，和 Gaussian elimination 類此。首先把 leave the basis 的那一行 scale 了讓該 $1$ 的地方 $1$ ，然後用減法所有該 $0$ 的地方 $0$ 就好了。

當你做完這個做 identity 的時候，你就會發現 reduced cost 的 constraint 會變成新的 reduced cost form！為甚麼？你剛把新的 basic column 的 variable 做 $0$ 了！新的 reduced row 自然還是 non-basic variable 的 linear combination！這樣我們又可以來一個新的 iteration 了。

這些iteration 自然不可能一直做 ⋯ 它有兩個情況下會停

- reduced cost 全部都是正數，這時我們無法再 improve objective 了，我們找到了 optimal！或者
- 所有 basic variable 的 adjustment 都是加，換句話說，我們可以無限減少 objective，這個 program 是 unbounded。

當我們還在 optimize $y$ 的時候，unbounded 是不可能的，因為 $1^Ta$ 最小是 $0$ ，但我們其實並不想要 $a$ 的最優值。

假如最優的 $a$ 正好是 $0$ ，那麽我們可以把和 $a$ 有關的都扔掉 (i.e $Ax + a = b$ 變成 $Ax = b$ ，和刪掉 $-y + 1^T a = 0$ 這個 constraint，把 objective 由 $y$ 換成 $z$ ），這個時候我們就有了一個 basic solution 和 $-z + c^Tx$ 的那一行也剛好變成了 reduced cost form，我們繼續 iterate 就好了。

相反，假如最優的 $a$ 不是 $0$ ，換句話說，無法使 $Ax=b$ ，則 linear program 本身 infeasible，可以直接返回了。

# Dual Interpretation
在算 reduced cost 的時候，我們會不停的在 reduced cost row 加其它 constraint row scaled，如果我們記住總共哪一個 row 減了多少倍，我們就有了 dual solution $y$ ！

- 留意我們有 $m$ 個 constraints，所以 dual 有 $m$ 個 variables，check。
- $c-y^T A$ 就是 reduced cost，它們全是非負數，feasible check。
- $-y^Tb$ 就是 $b$ column 的值，就是 $-z$ ，optimal，check。

我們剛剛證了 strong duality！在 primal feasible 的情況下，dual feasible 而且 dual 的 optimal 和 primal 的 optimal 一樣！

另外，我們留意到 reduced cost 只有在 non-basic variable 上才是非 $0$ ，這個叫做 complementary slackness，它的意思是當一個 dual constraint 不是 tight 的時候 (i.e. reduced cost 是非 $0$ )，對應的 primal variable 必須是 $0$ (i.e. non-basic)。

complementary slackness 也有要求 primal constraint 不是 tight 的時候 dual variable 必須是 $0$ ，但因為 standard form 的 primal constraint 都是 tight 的，所以這個條件會 trivially 被滿足。

- **TODO**: 試一次
