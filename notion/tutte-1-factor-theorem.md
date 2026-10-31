---
title: "Tutte’s 1-factor theorem"
source: "https://app.notion.com/p/7312618c6efc4ee5ab7ffc077d7a11b5"
---

這條圖論定理是 perfect matching 存在的充份必要條件，這篇筆記記錄讀這條 theorem 的一些細節。

## 定義

regular graph - 一個每個 vertex degree 都一樣的 graph。

spanning subgraph - 這個 subgraph 有齊原 graph 的所有vertex。

k-factor 是一 k-regular spanning subgraph。

例子：1-factor 就是 perfect matching

其它用到的常用定義假設熟悉，不寫了。

## Tutte's condition

對於所有 vertex subset $S$ ， $|S|\ge o(G-S)$ ，其中 $o(G)$ 的意思是 number of connected components with odd number of vertices。

例：graph 是五角形， $S = \phi$ ， $o(G-S)=1$ ，所以不符合 Tutte’s condition。

例：even clique，如果 $S=\phi$ ，則 $o(G-S) = 0$ ，這個沒問題。否則 $|S| \ge 1$ ，但因為 $G$ 是 clique，所以 $G-S$ 只有一個 connected component， $|S| = 1 \ge o(G-S)$ 也沒問題，even clique 符合 Tutte’s condition。[^clique-subset-size]

## Tutte's theorem

一個 graph 有 1-factor (i.e. perfect matching) 的充份必要條件就是 Tutte’s condition。

### The necessary condition is obvious

如果一個 graph 有 perfect matching，隨便抽一個 $S$ 出來，剩下的有幾個 connected component，其中 odd 的 connected component 沒有選擇，有 perfect matching 中必然有最少一條 odd component match 到 $S$ 的 edge。因為這些 edges 是 perfect matching，所以必然對應 $S$ 裡面不同的點，所以 Tutte’s condition satisfied。

### TONCAS: The obvious necessary condition also sufficient

反證法 ⋯ 如果有一個 graph $G$ 它有 Tutte’s condition 但沒有 perfect matching 會怎麼樣？

首先，你可以加 edge 進 $G$ ，對於任何的 $S$ ，加 edge 有幾點可能性。

1. edge 在 S 裹
2. edge 只有一端在 S 裡
3. edge 兩端都在同一個 G - S 的 connected component 裡
4. edge 的兩端分別在不同的 G - S 的 connected component 裡，兩個都是 odd
5. edge 的兩端分別在不同的 G - S 的 connected component 裡，其中一個不是 odd

我們可以一個一個 case 看，每一個 case 裡面的 odd component 數量都不會增加。明顯 1,2,3 都不會改變 connected components 的結構，case 4 只會減少 odd component，case 5 odd component 數量保持不變。

所以你隨便加 edge，原來有 Tutte’s condition 的 $G$ 加了 edge 後還是會有。

但一個 graph 沒有 perfect matching，加 edge 後就可能有了。最簡單的一個 case，只有兩點沒有 edge 當然沒有 perfect matching，但加一條 edge 就有了。

明顯，如果你把一個 graph 不停加 edge，最終它必然會變成 clique，even clique 必然有 perfect matching。

> Odd graph 其實就不用討論了，明顯不可能符合 Tutte’s condition。

所以我們可以想像這樣的一個 algorithm。

```txt
起點：G satisfy Tutte’s condition，G 沒有 perfect matching

while 存在 edge e such that G+e 沒有 perfect matching
  G = G + e
  
結果：G satisfy Tutte’s condition，G 沒有 perfect matching，
     而且 G 加任何 edge 都會有 perfect matching。
```


> 把 counter example 做到某程度上的 extremal 是 graph theory 中的常用技倆。

接下來，我們會給 $G$ 找一個 perfect matching，這樣就可以找到我們的矛盾了。

首先，我們把 $G$ 裡面 degree = $(n - 1)$ 的 vertex 全部抽出來叫作 $S$ 。

我們先解決簡單的 case，假如 $G - S$ 的 connected component 全部都是 clique，其實就很好找 perfect matching。For even components，隨便 match 就可以了，for odd component，也是隨便 match 就可以了，它總會剩下一個的，和 $S$ 裡面隨便 match 就可以了，Tutte’s condition 保證 $S$ 有足夠的點用，最後 $S$ 只能剩下偶數個點， $S$ 也是個 clique，所以也是隨便 match 就可以了。

最後剩下的 case 最難， $G - S$ 的 connected component 不全是 clique，那就可以找到 $x$ 和 $y$ 這兩個 vertices， $x$ 和 $y$ 在 $G - S$ 的同一個 connected component 裡面，但是 $x$ 沒有連著 $y$ ，所以 $x$ 到 $y$ 的 shortest path 最少有三個 vertices，就叫它 $x, a, b$ 好了（ $b$ 有可能是 $y$ ，也有可能不是，這並不重要），明顯 $x$ 不可能連著 $b$ ，否則 $x, a, b$ 不可能是 shortest path。

另外 $a$ 不在 $S$ 裡，所以 $a$ 不可能連上所有其它 vertices，總有一個 $c$ 是 $a$ 沒有連上的。

記得 $G$ 只要加任何一條 edge 都會有 perfect matching，現在我們加上 $(x, b)$ 就可以得到 matching $M1$ ，加上 $(a, c)$ 就可以得到 matching $M2$ 。明顯 $M1$ 會用到 $(x, b)$ 而 $M2$ 會用到 $(a, c)$ 。

這個時候從 $c$ 出發，由 $(c, a)$ 開始走，一步用 $M2$ ，一步用 $M1$ ，一直走，會怎樣呢？

留意 $M1$ 和 $M2$ 都是一個 matching，所以其實沒有選擇，每到一步都只有一個 choice。但個 graph 只有 finite 個點，所以結果一定會返去一個已經 visit 過嘅 vertex。

這個回去的時 vertex 只能是 $c$ ，因為除了 $c$ 之外，條 path 上面嘅所有 vertices 都被兩個 matching 都 match 過了，根本沒有可能有第三條 edge 能進去。而且回去 $c$ 的 edge 只能是 $M1$ ，cycle 的 number of edges 是 even。

如果這個 cycle 沒有使用 $M1$ 裡的 $(x, b)$ ，這個時候我們只要保留這個 cycle 之外的所有 $M2$ 的 edges，加上這個 cycle 裡所有 $M1$ 的 edges，這就是 $G$ 的 perfect matching。

> 它是 perfect matching 的原因是所有 vertices 都 matched，而且沒有使用 $(a, c)$ 和 $(x, b)$

否則 $c$ 總會先到 $x$ 或者 $b$ ，我們假設它先到 $x$ ，當然，到達 $x$ 的 edge 只能是 $M2$ ，因為下一步是 $M1$ 的 $(x, b)$ 。這時候的我們有一條 path 從 $a$ 開始使用 $M1$ ，到使用 $M2$ 到 $x$ 結束。

我們保留所有這條 path 之外 $M1$ 的 edge，在這條 path 裡，使用 $M2$ 的 edge，再加上 $(x, a)$ ，這就是 $G$ 的 perfect matching。

在先到 $b$ 的情況是一樣的，只不過最後加的是 $(a, b)$ 。

所以在任何情況下，我們都在 $G$ 找到 perfect matching，這就完成了我們的矛盾，證出 Tutte’s theorem。

Cecilia,

28th, May, 2024

[^clique-subset-size]: **勘誤：** 非空 `S` 不一定只有一個 vertex，這裡應是 `|S| >= 1 >= o(G-S)`。另外 `S = V(G)` 時，`G-S` 是 empty graph，沒有 connected component；所以一般應說「至多一個 connected component」。Even clique 符合 Tutte’s condition 的結論仍然正確。
