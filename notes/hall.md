# Hall's marriage theorem

Reference: [Wikipedia](https://en.wikipedia.org/wiki/Hall%27s_marriage_theorem)

兩個 equivalent 的 statements。

## System of Distinct Representatives

有一個 finite familiy of sets $F$ ，如果每一個 subset S 都有 $\left|\bigcup\limits_{s\in S} s \ \right| \ge \left| S \right|$ ，則可以從 $F$ 中的每一個 set 中抽一個 representative 令每一個 set 的 representative 都不一樣。

## Graph matching

有一個 bipartite graph $G = L + R$ ，如果 對於所有 $S \subseteq L$ ，
 $\left|\bigcup\limits_{l \in S} N(l) \ \right| \ge \left| S \right|$ ，則 $G$ 存在 perfect matching。

首先設 $L ＝ F$ , $R =$ $\displaystyle\bigcup\limits_{f \in F} f\ $，相對每一個 $(l, r)，l \in L, r \in R$ ，它是一條 edge if and only if $r \in l$ ，這就可以使兩個 statement 等價。

## Proof

Perfect matching 其實就是 maximum flow

如果有 Hall's condition 而 maximum flow 沒有用䀆所有的 $L$ 。

由任意 unmatched $l_1$ 出發，by Hall's condition， $l_1$ 一定有最少一個 neighbor $r_1$ ，如果它的 neighbor 是 matched 找它的左手邊，現在 $\{l_1, l_2\}$ 一定有兩個 neighbors，減去 $r_1$ 必然有 $r_2$ ，一直這樣做下去，它不可能無限，所以必然要停，停的時候只能是 $r_k$ 是 unmatched。到這就可以了，找到 augmenting path，matching 不是 maximum，矛盾。

## 例題

Wiki Deck of card 題： $L$ 是 pile $R$ 是 rank。一個 rank 最多只有 4 張， $k$ 個 pile 有 $4k$ 張必然有最少 $k$ 個 rank，Hall's condition satisfied。

Regular bipartite graph case，看著好像不好證 Hall's condition，因為 given 左手邊，你唔肯定右手邊會不會 union 不夠大。用 deck of card 想就通了，如果左手邊有 $k$ 個 nodes，就會有 $kd$ 個 neighbors(正在想 cards)，可重覆，但一個 node 要重覆，它必然來自不同的 $l \in L$ ，但最多只就只能重覆 $d$ 次，所以 Hall's condition 得證。

Group 題，類比 left coset 做 pile，right coset 做 rank，和 deck of cards 一樣。

功課見過一題

假設有 1 2n x 2n 的黑白棋盤，隨意在一黑一白兩個格子畫個叉不許放，證明剩下的格子可以用 2 x 1 骨牌填滿。

答案：所謂填滿，其實就是黑白之間有 perfect matching，所以要找 Hall's condition。

由於叉是隨意畫，其實要證 Hall's condition 並不明顯。

用貪吃蛇玩法，可以找到一條 Hamiltonian Cycle，以下是一個 4 x 4 的例子由 0 走到 F，不難想像任意大小的棋盤都能做到。

```
0FED
167C
258B
349A
```

隨意畫了兩個叉後，這個 Hamiltonian Cycle 就會被剪成兩段，兩段都是偶數長度。這樣 Hall's condition 就有了，因為每一個黑的，都能對應到一個 distinct 的白的。

