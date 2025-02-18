# max-flow min-cut

其實一早應該寫這一篇，它應該是 hall 和 konig 的 prerequisite，但是我一直沒有寫，所以現在來補上。

Reference: [Wikipedia](https://en.wikipedia.org/wiki/Max-flow_min-cut_theorem)

## max-flow problem

Given 一個有向圖，edges 上有 capacity，要找出從 source 到 sink 的最大流量。

你可以從 source 流出任意數量的流量，但是每條 edge 上的流量不能超過 capacity，而且每個點的流入流出量要平衡，最後 sink 可以接收的流量就是最大流量。

## min-cut problem

在上面一樣的圖和 capacity，找到一個 partition，使得 source 和 sink 在不同的 partition 中，且 partition 中的 edge capacity 總和最小。

## max-flow min-cut theorem

max-flow 和 min-cut 有一個很重要的性質，就是 max-flow = min-cut。

## Observation

首先，min-cut 明顯一定不能比 max-flow 小。考慮 min-cut 上有一個 flow，那麼這個 flow 必須流過 min-cut，這個 flow 不可能比 min-cut 大，否則就會超越 capacity constraint，所以 min-cut 明顯一定不能比 max-flow 小。

所以這又是一個 duality 問題，minimization 的答案和 maximization 的答案一樣。

## Proof

首先介紹 residual graph。假設我們已經有一個不一定是 maximum 的 flow，我們做一個反悔圖，就是說如果你從 $u$ 到 $v$ send 了一些 flow $f$ ，你可以反悔，不 send 了。這種反悔，可以用一條由 $v$ 到 $u$ 的 edge 來表示，這條 edge 的 capacity 就是 $f$ 。我們先把用了的 capacity 扣除，刪掉 0 capacity 的 edges，然後把這些反悔的 edge 加到原來的圖上，就得到了 residual graph。

假如我們能夠在 residual graph 上找到一條由 source 到 sink 的 path，那麼我們就可以通過這條 path 來增加 flow。所以在 max-flow 的 residual graph，source 和 sink 之間不可能有 path。

從 source 開始出發，在 residual graph 裡做搜索，結果會找到一堆不包括 sink 的 nodes，就叫它 $S$ 好了，剩下的就叫 $T$ 。

 $S$ 和 $T$ 是一個 cut，我們看看這個 cut 的 capacity。

如果有任何一條 edge 在原有的圖中由 $S$ 連向 $T$ ，而在 residual graph 中這條 edge 已經不存在了，所以只能是它原有 capacity 全部都用完了，所以我們找到一個 cut 它的 capacity 正好是 max-flow。

可以了，因為 min-cut >= max-flow，而我們又找到一個正好等於 max-flow 的 cut，所以 min-cut = max-flow。

## Algorithm

上述的證明同時也給了我們一個找 max-flow 的 algorithm，就是不斷的在 residual graph 上找 source 到 sink 的 path，然後增加 flow。Max-flow min-cut theorem 正好證明了這個方法的正確性。

這個算法叫做 Ford-Fulkerson method，叫做 method 是因為我們沒有指它找 path 的方法。如果我們是用 BFS 來找 shortest path 來 augment，這個算法叫 Edmonds-Karp algorithm。作為 Ford-Fulkerson 的一個特例，Edmonds-Karp 明顯正確，下面我們證一下它的 complexity。

Reference: [Wikipedia](https://en.wikipedia.org/wiki/Edmonds%E2%80%93Karp_algorithm)

## Complexity

在 CLRS 上有一個 Edmonds-Karp 的 complexity 證明。Edmonds-Karp 的 time complexity 是 $O(VE^2)$ ，在這裡我重寫一次。

### Lemma: Mono-increasing property

首先是一個 Lemma，在 Edmonds-Karp 的 residual graph 中，對於每一個 node $v$ ，source 到 $v$ 的 shortest path 長度不會減少。

這裡我們會用到一個圖論中的經典技巧，minimal counter example。

假設我們一直 monitor Edmonds-Karp 的執行，第一次發現有一個 node $v$ ，它在 augment 後的 shortest path 比 augment 前短。

為了讓以下的討論更方便，我們先給一些東西名字。

- $v$ 是 counter example node
- 發現 counter example 前的 flow 是 $f$ , residual graph 是 $G_f$ 
- 發現 counter example 後的 flow 是 $f'$ , residual graph 是 $G_{f'}$ 
- $d(v, f)$ 是 source 到 $v$ 的在 $G_f$ shortest path length

source 到 source 的 shortest path length 永遠是 $0$ ，所以 $v \neq source$ 。 $v$ 在 $G_{f'}$ 的 shortest path 必然有一個 parent。我們把這個 parent 命名為 $u$ ，所以 $d(v, f') = d(u, f') + 1$ 。

接下來，我們看看 $(u, v)$ 這條 edge。它明顯在 $G_{f'}$ 中存在，要不然 $u$ 不可能在 $G_{f'}$ 的中做 $v$ 的 parent。它有可能在 $G_f$ 中存在嗎？

假設 $(u, v)$ 在 $G_f$ ，可以用這個方法找到矛盾：

$$\begin{align*}
d(v, f) &\le d(u, f) + 1  \\
&\le d(u, f') + 1 \\
&= d(v, f')
\end{align*}$$

- 第一行是三角不等式，shortest path distance 是一個 metric，所以 $||s, v|| <= ||s, x|| + ||u, v||$ 。
- 第二行是因為 $v$ 是第一次發現的 counter example，所以 $u$ 不是一個 counter example，即 $d(u, f) \le d(u, f')$ 。
- 第三行是因為 $u$ 在 $G_{f'}$ 是 $v$ 的 parent，所以 $d(v, f') ＝ d(u, f') + 1$ 。

這是一個矛盾是因為我們假設 $v$ 是一個 counter example，所以 $d(v, f') < d(v, f)$ ，但我們證到和假設相反的結果。

假設 $(u, v)$ 不在 $G_f$ ，我們一樣可以找到矛盾：

如果 $(u, v)$ 不在 $G_f$ ，但 $(u, v)$ 在 $G_{f'}$ ，那麼上一個 iteration 我們一定是把 flow 從 $v$ 送到 $u$ 。但 Edmonds-Karp 只會找 shortest path 來 augment，所以在 $G_f$ ， $v$ 是 $u$ 的 parent。

$$\begin{align*}
d(v, f) &=   d(u, f) - 1  \\
&\le d(u, f') - 1 \\
&=   d(u, f') + 1 - 2 \\
&=   d(v, f') - 2 \\
&<   d(v, f')
\end{align*}$$

- 第一行是因為在 $G_f$ ， $v$ 是 $u$ 的 parent。
- 第二行是因為 $u$ 不是 counter example，所以 $d(u, f) \le d(u, f')$ 。
- 第四行是因為在 $G_{f'}$ ， $u$ 是 $v$ 的 parent，所以 $d(v, f') = d(u, f') + 1$ 。
- 這是一個矛盾是因為我們又證到和假設相反的結果。

所以我們證明了 mono-increasing property。

### Theorem: Edmonds-Karp complexity

在想 Edmonds-Karp 的 complexity，最主要就是做了多少次 augment。而 augment 的次數不是特別好數，是因為 edge 有可能來回的反悔，所以我們可以看一下如何去 bound 反悔的次數。

比如說 $(u, v)$ 這條 edge augment 滿兩次。

- 第一次 augment 的時候，我們設 $d(u, f) = k$ ， $d(v, f) = k + 1$ ，然後 $G_{f'}$ 就沒有 $(u, v)$ 這條 edge 了。
- 第二次 augment 之前，必然有一次 augment $(v, u)$ ，設這個時候的 flow 為 $f''$ ，monotone increasing lemma 說明了 $d(v, f'') \ge k + 1$ ，所以 $d(u, f'') \ge k + 2$ 。
- 到第二次 augment 的時候，設 flow 為 $f'''$ ， $d(u, f''') \ge k + 2$ 。

一個 node 的 distance from source 最多只能是 $|V| - 1$ ，所以一條 edge 最多只能 augment 滿 $\frac{|V|}{2}$ 次。

每一次 augment 總會最少 augment 滿一個 edge，每個 edge 只能 augment 滿最多 $\frac{|V|}{2}$ 次，所以 augment 的次數最多是 $O(VE)$ 。而每次 augment 的時間是 $O(E)$ ，所以 Edmonds-Karp 的 time complexity 是 $O(VE^2)$ 。

留意 Edmonds-Karp 其實只是一個相對簡單的 max-flow algorithm，我們有更好的算法，比如說 Dinic's algorithm，這篇先不講了。

