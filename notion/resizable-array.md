---
title: "Resizable Array in Optimal Time and Space"
source: "https://app.notion.com/p/83546a109b9147d8b67e1b97f317d6c0"
---

[https://cs.uwaterloo.ca/research/tr/1999/09/CS-99-09.pdf](https://cs.uwaterloo.ca/research/tr/1999/09/CS-99-09.pdf)

## 為甚麼讀這篇論文？

在研究網友 Aoi 的提問的時候，偶爾搜到了有關 Asymptotically optimal algorithm 的 wikipedia page，奇怪地這個 page 只有這個 reference。看著題目有趣，就讀了。果然是一篇有趣的論文。

Array 可以說是最簡單的 data structure，就算是 resizable，無非就是 vector 或者是 deque，有甚麼寫的意義呢。留意這份 paper reference 了一份 1999 年的 paper，所以最少是 1999年或之後寫的，當時 vector 和 deque 的理論應該已經相當成熟

> 吐糟一秒 ⋯ 當年不成熟的是 MSVC 的 STL implementation，扯遠了 ⋯

明顯，搶眼的是 optimal 這個字，這些操作都是 O(1)，難不成還能有比 O(1) 快的？

原來關鍵是 space 和執行速度上的挑骨頭。

## Space optimality

論文用了一個很巧妙的方法證了額外的空間最少需要 $O(\sqrt{n})$ 。

前題是使用 memory allocator 的情況下，每一次的 allocation 必然有一個 constant size 的 overhead。

整個 rough idea 是如果你想省 overhead，你的 allocated block 就會大，在剛 allocate 完沒使用上的時候 overhead 就會大。

相反如果 allocate 的 block 很少，就會有很多 block，block 要求的 overhead 就會大。

正式證明，如果最大的 block size 是 $S$ ，block 數是 $K$ ，block overhead 是 $h$ 。

因為你要有能力 store $n$ 個 number，所以 $SK \ge n$

overhead 是 $\max(S, Kh)$ ，因為剛 allocate $S$ 沒用上，或者 $K$ 個 block 的 $Kh$ overhead

因為 $SKh \ge n$ ，所以 $\max(S, Kh) \ge \sqrt{n}$ ，也就是說 overhead 最少是 $\Omega(\sqrt{n})$ 。

> 留意我們可以假設 S 是 overhead 是因為我們假設 realloc 變成了 alloc。否則不停 realloc 加 1 個 element 的話可以做到 $O(1)$ overhead，但以這一段來說論文理解它為 $O(n)$ overhead，換句話說，realloc 中有可能暫時用到的 extra memory 它都算了。

# 首先復習一下 doubling array

論文假設 alloc/free/realloc 是 $O(1)$ 。

> 又吐糟一秒，這很假吧 ⋯ Anyway，論文就是這樣假設

首先復習一下 doubling array，原理很簡單。用一個 array，每次需要爆的時候 realloc 一個 double 的 size。假設 insertion 和 deletion 只能在尾巴發生。

如果跟論文的 assumption，realloc 是 $O(1)$ ，其實這就完了，問題當然是這樣做的 extra space 是 $O(n)$ 。

那為甚麼要 double size 呢？因為 realloc 根本不是 $O(1)$ ，而是 $O(n)$ 。假設我們每次加一個 element 都要 realloc，那麼每次都要 $O(n)$ 時間去做 insertion，很不划算。

如果我每次做到 $2^n$ 才去 resize，那麼每一次 resize 到 $2^n$ 前必然有最少 $2^{n-1}$ 次 insertion (可能更多，你可以不停 insert/delete），所以你可以把 resizing 的時間當成是 insertion 預付。

Concretely，我們假設 insert 的時候要交 $I$ 個金幣 (要 realloc 的 case 多交一個)，寫東西進去 array 要用 1 個金幣，realloc 一個 size $n$ 的 array 去更大的 size 需要 **$n$** 個金幣。 $1$ 個用來 realloc， $n$ 個用來 copy。

所以 idea 是要在每一個 array entry 上保留金幣留待以後 copy 用。

我們的 scheme 是這樣的，每個 entry 要求最少一個金幣﹐到了要 realloc 時，每個必須要有兩個。

假設一開始我們就有 array size = 1

第一次 insert 交了 $I$ 個金幣，一個用來寫 array，兩個留在 array\[0\] 上。所以 $I$ 最少是 3。必須留兩個的原因是下次必須 realloc 了。

再二次 insert 交了 $I + 1$ 個金幣，這次 realloc 需要用掉一個，copy 用掉一個，寫 array entry 要用掉一個，還要所有 array entries 必須有兩個金幣，因為下次又要 realloc 了，所以我們總共需要 1 + 1 + 1＋4 = 7 個金幣，這時我們兩個金幣在 array\[0\]，加上第二次交的 $I + 1$ 個金幣，所以 $2 + I + 1 \ge 7$ ，換句話說 $I \ge 4$ 。

其實每一次 realloc 到 $2^n$ 之前，會有 $2^n + I + 1$ 個金幣可以用﹐realloc 用掉 $1$ 個，copy 用掉 $2^{n-1}$ 個，寫 array 用 $1$ 個，留在新 array entry $2$ 個。

重點來了，這個時候我選擇前面的其中一個加 $1$ 個金幣。

每一個 array 可以分成兩個半個，前面半個是填滿的，後面半個正在填。

前面半個每一個 entry 都有最少一個金幣，那是 resize 給它留下的。後面有幾個 entries 填了，後面的 entry 全部都有 2 個金幣，而前面也會有對應個 entries 有 2 個金幣。

明顯 4 個金幣足夠保留這個 invariant，一個用來寫，兩個留在自己，一個找前半段放。

到需要 resize 的時候，後半段填滿了，前半段的金幣也就存夠了，realloc 後 invariant 還能保持。

所以其實 進行 $n$ 個 insertion 的 total cost 也就少於 $4n$ ，這是我們可以說 insertion 的 cost 是 amortized $O(1)$ 。當然，某個倒楣的 insertion 要用 $O(n)$ 時間那是逃不掉的，預付不等於時間就真的後來免費了。

Removal 其實也可以同樣的原理，用不去 realloc 的 removal 預付 realloc 的時間。而且 realloc 縮少在一些 allocator 中是免費的，不用 copy，所以應該更簡單。但有一點很重要‼️上面的分析是已 realloc 必須要有過半之 insertion 預付它，所以如果 removal 導致一個全滿的 array，下一個 insertion 就立刻 realloc 的話，就會破壞了這個 property。

換句話說，removal 的 realloc 必須保證剩下的 array 最多半滿，再換句話說，removal 要等到 array 只有 1/4 滿的時候才能縮一半，再換句話說，最糟的情況是非常接近只有 1/4 滿， $3n$ 個 element overhead，store 100M data 要用 400M。

太大了，躲在 $O(n)$ 後的 overhead 是三倍！

在這裡預告一下 4.3 會講的個技巧，這裡我的分析是預「付」，但代價是真的要做的時候，還是會需要 $O(n)$ 時間，要不我們把將來要做的工作先做了呢？

Concretely，我們假設 initial size 是 2，我們預做要 resize 變小和變大的 array，所以任何時候我們都有 3 個 array。

第一個小 array 的 size 是 1，就是要被 half 的 array。

第二個中 array 的 size 是 2，就是真的 array。

第三個大 array 的 size 是 4，就是要被 double 的 array。

當我們寫 array 時，我們可以只寫小的和中的，只有當小的滿了之後，才同時寫中的和大的，而且寫大的要寫兩個，這樣就可以保證中的滿了的時候，大的剛好能用。

還是用例子吧，假設我們只是很簡單的寫 1,2,3, …

```txt
Insert 1
Small  : [1,?]
Medium : [1,?,?,?]
Large  : [?,?,?,?,?,?,?,?]

Insert 2
Small  : [1,2]
Medium : [1,2,?,?]
Large  : [?,?,?,?,?,?,?,?]

Insert 3
Small  : [1,2]
Medium : [1,2,3,?]
Large  : [1,?,3,?,?,?,?,?]

Insert 4
Small  : [1,2]
Medium : [1,2,3,4]
Large  : [1,2,3,4,?,?,?,?]

Insert 5:
Small  : [1,2,3,4]
Medium : [1,2,3,4,5,?,?,?]
Large  : [1,?,?,?,5,?,?,?,?,?,?,?,?,?,?,?]

Insert 6
Small  : [1,2,3,4]
Medium : [1,2,3,4,5,6,?,?]
Large  : [1,2,?,?,5,6,?,?,?,?,?,?,?,?,?,?]
```

很明顯這樣做可以保證大 array 可以在中 array 滿的時候升級變成半滿的中 array。而且這個 algorithm 保證 $O(1)$ 最多加一個 allocation 操作。問題當然是 overhead 更多啦，看看我們 insert 5 之後總共用了 28 個 slot，但 array size 只是 5。

# 機會來了 ⋯

我們需要 doubling array 只不過是因為我們堅持只使用一個 block，如果我們願意用多於一個 block，就可以做到 realloc 只花少於 $O(n)$ 時間去 copy。

當然，有多於一個 block，我自然要花時間去找某一個 element 到底在哪一個 block，所以也不能太複雜。

論文中一個比較簡單的 scheme 是 block size 是 1,2,3 這樣遞增。

當然，我們需要一個本身也是 resizable 的 array 去 裝這些 block 的 address。

不難算出我們只會有 $O(\sqrt{n})$ 個 block，所以裝 block address 的 resizable array 可以用普通 doubling array 去做，最終 extra space 也不過是 $O(\sqrt{n})$ 。

第一個 block 有一個 element，第二個 block 有兩個 elements，所以如果我有 $n$ 個 blocks，我就有 $\frac{n(n+1)}{2}$ 個 elements。反向看，第 $\frac{n(n-1)}{2}$ 到第 $\frac{n(n+1)}{2} - 1$ 號 elements 在第 $n$ 個 block，所以這其實只是在 solve[^quadratic-root]

$$
\begin{aligned}
\frac{n(n-1)}{2} &= k \\
n^2 - n - 2k &= 0 \\
n = \frac{-2 \pm \sqrt{1 - 8k}}{2}
\end{aligned}
$$

當然算出來的 n 不一定是整數，那當然是因為 $k$ 不一定是 block 裡的第一個，用一下 ceil 就好了。[^block-rounding] 算好第幾個 block 後，算在 block 裡的第幾個就簡單了。

# 那也不成嗎？

留意要找到第幾個 block，要算 square root，如果 square root 可以算 $O(1)$ ，這個問題就搞定了。

實際上，算 square root 要用到 Newton’s algorithm，需要 $O(\log \log k)$ 時間，呃 ⋯ 這就有點挑骨頭了。

論文下一步介紹另一個方法，使用 bit shift 之類的 operator 去做。

它提議了這個方法去分 blocks

| Super Block ID (i) | Number of Blocks (n) | Block Size (s) |
| --- | --- | --- |
| 0 | 1 | 1 |
| 1 | 1 | 2 |
| 2 | 2 | 2 |
| 3 | 2 | 4 |
| i | $2^{\lfloor \frac{i}{2}\rfloor}$ | $2^{\lceil\frac{i}{2}\rceil}$ |

它使用了 super block 把 block 組織起來，super block 其實並不存在，只是一種我們理解的方法，每一個 super block 有 exactly $2^i$ 個 elements，block size 的設計是大約為 super block size 的 square root。

Block size 是 square root 是關鍵，這樣我們可以保證 overhead 是 square root。

要做 insertion，如果 last block 還有空位就沒有問題，否則按上述方法做新 block，然後用就好。

要做 deletion，如果 block 還沒有清空，甚麼都不做，只有當我們有兩個 empty block 的時候，才刪一個。

最難的是算第 k 個 element 在哪

我們留意到要知道第 k 個 element 就哪一個 super block 可以簡單的用 msb，我們假設 array 從 1 開始

| Index Range | Super Block |
| --- | --- |
| 1-1 | 0 |
| 2-3 | 1 |
| 3-7[^superblock-range] | 2 |

我們要快速的算出在第幾個 super block，論文假設這只是 bit shift 所以 $O(1)$ ，我這點我不很認同，雖然 x64 確實有 BSR instruction 可以用一個 instruction 直接算出，但 x64 也有 sqrtss 可以快速算出浮點 square root ⋯

一旦知道了在哪個 super block，算算前面有幾個 elements 就不難了，只是 sum of GP。剩下的數一下有幾個 block 和在 block 裡的哪一個就可以了。這些 sum of GP 和 div mod 算都應該是 $O(1)$ 。

# 應用

這個東東用來做 stack 最簡單了，本來它就能隨意在尾巴加減元素。

這個東東還能用來做 priority queue，原理一樣，那些 bubble-up/bubble-down 操作只需要隨機讀寫，這個東東能做。

這個東東用來做 queue 就有一點挑戰了，它的靈感來源於 functional programming。

在 functional programming 的世界裡，queue 可以通過兩個 stacks (i.e. con cells) 來完成。

一個叫做 enqueue stack，enqueue 的時候 push 到 enqueue stack 裡。

一個叫做 dequeue stack﹐dequeue 的時候往 dequeue stack 裡 pop，所以 dequeue stack 的頂應該是第一個 element。

但 dequeue stack 本來是空的，所以在 dequeue 前，我們可以把 enqueue stack 裡的元素一個一個的 pop，再一個一個的 push 到 dequeue stack 就好。

分析，每一個元素最終只會進出 enqueue stack 和 dequeue stack 最多四次，所以 amortized analysis 還是每個操作是 $O(1)$ 。 

想換成 worse case O(1)，我們可以利用 random access 的特性，我們保持原有的 enqueue stack，但在 dequeue stack 上下手腳。

首先，假如 dequeue stack 是空的，swap 一下 dequeue stack 和 enqueue stack 就好，現在我們有一個 dequeue stack 了，唯一的問題，是 dequeue stack 裡面元素的方向是反的，不能用 pop 拿 result。這時候，我們用 random access，還是能夠拿到 dequeue stack 頂的，但之後怎麼辦呢？

反正只能操作尾部，把尾部的 element pop 掉填首位空間就好了。

這個還是便用例子去解釋會比較簡單，假設 insertion 的順序是 1,2,3 …

```txt
Dequeue Stack: [1,2,3,4,5,6,7,8]
                *
                
Dequeue Stack: [8,2,3,4,5,6,7]
                  *
                  
Dequeue Stack: [8,7,3,4,5,6]
                    *
                                      
Dequeue Stack: [8,7,6,4,5]
                      *
                                      
Dequeue Stack: [8,7,6,5]
                      
```


像這樣，在 pop 掉一半後，就會變成正確的順序，之後直接 pop 就好。

## 其它

這篇筆記忽略了論文中寫的 Buddy System 和 Deque，那二段比較難明白，我就放棄了，反正大的 idea 都明白了。

## 讀後感

算一下 square root 都嫌不是 $O(1)$ ，這有夠挑骨頭的 ⋯ vector 躲在 $O(n)$ 身後的 constant 可真不少啊！！！

Cecilia,

22nd, June, 2024

[^quadratic-root]: **勘誤：** `n^2 - n - 2k = 0` 的 roots 是 `(1 + sqrt(1 + 8k))/2` 和 `(1 - sqrt(1 + 8k))/2`，不是上面的式子。用來定位 block 的是正 root。

[^block-rounding]: **勘誤：** 按這一段的 zero-based element index 和 one-based block number，應是 `n = floor((1 + sqrt(1 + 8k))/2)`。因為第 `n` 個 block 的範圍是 `n(n-1)/2 <= k < n(n+1)/2`，所以要用 floor，不是 ceil。例如 `k = 2` 仍在第 2 個 block，ceil 卻會給出 3。

[^superblock-range]: **勘誤：** 這一個表改用了 one-based element index，super block 2 應包含 4 到 7，而不是 3 到 7；index 3 已經屬於 super block 1。
