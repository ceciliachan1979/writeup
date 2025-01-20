# Konig's Theorem

Reference: [Wikipedia](https://en.wikipedia.org/wiki/K%C5%91nig%27s_theorem_(graph_theory))

## Statement

在一個 Bipartite graph $G = L + R$，minimum vertex cover 和 maximum matching 一樣大

## Observation
明顯 vertex cover 要 cover 所有 maximum matching 的 edge，所有 minimum vertex cover 一定不能比 maximum matching 小。

這是一條類此 duality gap 的問題，一個 maximize 的結果等於另一個 minimize 的結果，如 linear programming strong duality，又或者 max flow min cut。

Turn out 確實可以用它們。

## Proof
在圖上加一個 source 指向所有的 $L$ ，所有 $R$ 指向一個新 node target。所有原 edge 的 capacity 是 $\infty$，所有新 edge capacity 是 1，在這個圖找 max-flow min-cut。

max-flow 是 maximum matching。

考慮 min-cut $(S, T)$ 的特性：
想象用 residual graph 做 searching，
首先由 source 出發，去 matched $L$ 的路已經用了，走不通，所以只能到所有 $L$ 的 unmatched nodes。
繼續走，會到 $R$ 的 nodes，這些 nodes 不可能是 unmatched 的，因為如果是 unmatched 就可以有 augmenting path 去 target。
matched nodes 不能去 target，因為 edge 已經用了。
留意因為我要找的是在 residual graph，所以雖然不能去 target，但有可能回去找一些 $L$ 的 matched node。
然後就沒有然後了 ⋯ 因為從這裡開始，我們不能去 $R$ 的 unmatched node （還是因為有 augmenting path），而對應的 matched node 已經走過了。

$S \cap R$ 有一個特性，就是它們會 cover 所有 unmatched edges，如果它們有機會通過 matched edge 回去 $L$，那麼那條 matched edge 也 cover 了。
$T \cap L$ 是 S 搜索不到 matched nodes，它們會 cover 所有剩下的 matched edge。

所以 $(S \cap R) \cup (T \cap L)$ 是一個 vertex cover。

找到 vertex cover 後，我們希望它的 size 和 min-cut 一樣。首先，min cut 不可能包括任何 $L$ 去 $R$ 的 edges，它們的 capacity 是 $\infty$。所以 min cut 其實只有 source 去 $L - S \cap L = T \cap L$ 和 $R - T \cap R ＝S \cap R$ 去 target，這樣看，就明顯得知 size of minimum vertex cover = size of min cut = size of maximum flow = size of maximum matching。

Wikipedia 還有兩個 proofs，用 alternating path 的那個其實根本和上面一樣，只是換一個方式講 $S \cap R$ 而已。而 Linear programming duality 我打算先跳過，先複習 linear programming duality 再算。

## NP-Complete?
在 general graph 找 minimum vertex cover 是一個 NP-Complete 的問題。和 Konig 定理可以用來解 Bipartite graph 的 minimum vertex cover 並不沖突。

順帶複習一下：

設 $C$ 為 graph $G$ 的 maximum clique
- $C$ 是 $\overline{G}$ 的 maximum independent set
- $\overline{C}$ 是 $\overline{G}$ 的 minimum vertex cover

## Vertex cover on tree
Tree 當然也可以看成 Bipartite graph，但要在 tree 上找 vertex cover，可以簡單一點用 dynamic programming 或者 greedy 就可以了。

詳見 [LeetCode - Binary Tree Camera](https://leetcode.com//binary-tree-cameras)

## TODO
Wikipedia 還有關於 coloring 和 weighted 的討論，還是以後再讀。