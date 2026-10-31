---
title: "讀論文 Selection in X+Y and Matrices with Sorted Columns and Rows 筆記"
source: "https://app.notion.com/p/cb6cd62322154a6e87f1f1cecb08894b"
---

[http://www.cse.yorku.ca/\~andy/pubs/X%2BY.pdf](http://www.cse.yorku.ca/~andy/pubs/X%2BY.pdf)

## 為甚麼要讀這一篇論文？

最近做題目碰到了這一題

K-th Smallest Prime Fraction

[https://leetcode.com/problems/k-th-smallest-prime-fraction](https://leetcode.com/problems/k-th-smallest-prime-fraction)

這篇文章主要是寫題目下方的挑戰，要求比 $O(n^2)$ 快

首先做到 $O(n^2)$ 再說

留意我們只有 $\frac{n(n-1)}{2}$ 個分數，我有 $O(n^2)$ 時間，所以有足夠時間把它們都 generate 出來。

用 quickselect 就可以找到第 $k$ 個了。Quickselect 是 expected linear time，所以 $O(n^2)$ 沒有問題。

但如何比 $O(n^2)$ 還要快呢？

在討論區中看到有人 reference 這一題

Kth Smallest Element in a Sorted Matrix

[https://leetcode.com/problems/kth-smallest-element-in-a-sorted-matrix](https://leetcode.com/problems/kth-smallest-element-in-a-sorted-matrix)

在這一題都下方有這份論文的連結

Selection in X+Y and matrices with sorted row and columns

[http://www.cse.yorku.ca/\~andy/pubs/X+Y.pdf](http://www.cse.yorku.ca/~andy/pubs/X+Y.pdf)

所以就開始了讀這一篇論文

## 問題介紹 ⋯

第一節沒有甚麼重要的內容，我們就暫時略過了，真正的內容從第二節開始，第二節給了我們一些定義

Ordered matrix 是我們用來做搜索的 matrix，它的特點是

1. rows are nonincreasing, and
2. columns are nondecreasing

一看就很繞 ⋯ 吐嘈一秒

第一，好多 negative，為甚麼非要反著說，monotonic decreasing 不好嗎，非要 non increasing ⋯

第二，不對稱，為甚麼 row 和 column 方向相反？

向右 或者 向上 是 monotonic decreasing

向左 或者 向下 是 monotonic increasing

又或者用一下符號 $A\_{(i+1)j} \ge A\_{ij}$ 和 $A\_{ij} \ge A\_{i(j+1)}$

這裡是一個例子

```txt
   5   3   3   2
   6   4   4   3
   6   4   4   3
   8   6   6   5
```

對了，我們只考慮方陣

問題是在這種 ordered matrix 裡面找 sorted 後的第 k 個﹐這份論文是以 1 為最小的 index，所以第 1 個是 2，第 2 個是 3，第 3 個也是 3，如此類推。

## Downsampling？

介紹完 ordered matrix 後，author 突然介紹一個 downsampling 的 operator，基本上只要單數行和列，然後如果是偶數的話也要最後一行和列 (這裡行和列使用的是台灣的標準，row是列，column是行)

按我們先前的例子， $\bar{A}$ 應該就是

```txt
5 3 2
6 4 3
8 6 5
```

就這個例子來說，這個 downsampling 好像沒有減少多少 ⋯ 但其實這是一個錯覺，大的 matrix 這個 downsampling 的過程大概可以刪掉大概 75% 的 entries。

Author 沒有寫，這裡我們算一下具體 matrix downsample 後的 size。

設 downsample 後 matrix 的 size 是 $m \times m$

如果 $n$ 是奇數， $m$ 就是 $\frac{n+1}{2}$ (e.g. $n=5 \implies m=3$)

如果 $n$ 是偶數， $m$ 就是 $\frac{n}{2} + 1$ (e.g. $n=6\implies m=4$)

所以可以得出 $m - \frac{n}{2} \le 1$ ，推出 

$$
\begin{aligned}
m   &\le 1 + \frac{n}{2} \\
m^2 &\le 1 + n + \frac{n^2}{4}
\end{aligned}
$$

所以 downsampling 確實可以把問題快速變小，可是 ⋯

## Downsampling 的問題

回歸主題，我們想在 ordered matrix 中找第 $k$ 個 element，可以假如 matrix 變小了，第 $k$ 個 element 還在變小了的 matrix 裡嗎（問題1）？它會是變小了的 matrix 的第幾個呢（問題2）？

問題 1 的答案是否 ⋯ 我們可以看看這個例子

```txt
 4  3  2  1
 8  7  6  5
12 11 10  9
16 15 14 13

```

不難驗證這是一個 ordered matrix，也不難找出 downsample

```txt
 4  2  1
12 10  9
16 14 13

```

然而明顯第 7 個數字 7 不在 downsample 裡。所以雖然問題變小了，可是再也解決不了原題目了。

那到底把問題變小有甚麼用呢？有了 QuickSelect，其實我們並不需要變小了的問題能夠直接找到答案，變小了的問題只要能把原問題中的 $O(n^2)$ 那麼多個 entries 變成一個 $O(n)$ 的 array 就可以了，有了這個較小的 array，就可以用 quick select 做完最後一步了。

算法速度分析將會是這樣

$$
T(n) = T(\frac{n}{2}) + O(n)
$$

所以最後 $T(n) = O(n)$

這一篇跳步講了第四小節的重點，但我覺得應該把這一步份提前講，因為一開始講 downsampling 和 rank 實在沒有 context 很難理解

> 上述 time complexity 的討論並不 formal，只是用來 motivate，明顯 $\frac{n}{2}$ 有可能非整數 ⋯

## Rank

當然，魔法在於利用 downsample recursively 幹了些甚麼事呢？這個時候我們就可以看看問題 2

首先我們先看看 author 介紹的另一個定義， $\text{rank}^-$ 和 $\text{rank}^+$ 。

Hmm ... 第 $k$ 個不是很清楚嗎？為甚麼要搞兩個新定義？問題在於重覆 ⋯

```txt
5   3   3   2
6   4   4  (3)
6   4   4   3
8   6   6   5

```

括號中的那個 (3) 可以是第二個，也可以是第五個 ⋯ 原來對著一個數值來看，它是第幾位是不好說的 ⋯ 當然，反過來我總是可以說第三個數值一定是 3，最少問題本身是 well defined 的。

粗略看，一個數值的第幾位無非就是一個 range，但 author 選擇用另外一種方法去表達它。

$\text{rank}^-(L, v)$ 是在 $L$ 裡面比 $v$ 小的數值的個數，比如說 $\text{rank}^-(A, 3) = 1$ ，因為只有一個 2 比它小，同樣道理， $\text{rank}^-(A, 4) = 5$ ，留意重覆的 3 是都算的。

同理， $\text{rank}^+(L, v)$ 是在 $L$ 裡面比 $v$ 大的數值的個數，比如說 $\text{rank}^+(A, 6) = 1$ ， $\text{rank}^+(A, 5) = 6$ 。

$\text{rank}^+$ 和第 $k$ 位是關係有一點點繞，其實也不過就是個減法

$\text{rank}^+(L, v) = k$ 和 $v$ 在 $L$ 裡最大是第 $|L| - k$ 位是等價的。比如說，比 6 大的只有一個數值，和說 6 最多第 16 - 1 = 15 位是一樣的。

如果 $v$ 這個數值是 $L$ 裡的第 $k$ 位，明顯 $0\le\text{rank}^-(L, v) \le k - 1$ （我前面全部和我一樣，或者全部比我小），同理 $0 \le \text{rank}^+(L, v) \le |L| - k$ （我後面的全部一樣，或者我後面的全部比我大）

有趣的是，假若這兩個條件同時是對的時候，那麼第 $k$ 位是數值必然是 $v$ 。

如果它比 $v$ 小，則有 $k$ 個 values 比 $v$ 小，contradict $\text{rank}^-(L, v) \le k - 1$ 。

如果它比 $v$ 大，則有 $L - k + 1$ 個 values 比 $v$ 大，contradict $\text{rank}^+(L, v) \le |L| - k$ 。

用這樣的方式，我們可以用 rank 去推斷第 $k$ 位的數值。

## Downsampling 和 Rank Bound

講了 $\text{rank}^-$ 和 $\text{rank}^+$ 的定義和性質，我們就可以開始閱讀第三節了，終於來講講 downsample 前後 rank的關係。

由於 downsample 本身會減少訊息，in general 我們不期望有 exact result，所以我們只有一個 bound，但夠了。

如何 bound 呢？

根據上面 algorithm 的想法，假設我知道 downsample 後某數值 $v$ 的 $\text{rank}^-$ 是 $x$ ，我能說明 downsample 前的情況嗎？

最簡單的 case 就是 downsample 前的 $\text{rank}^-$ 必然最少為 $x$ ，這個很明顯，在 downsample 前已經有 x 個比它小的數值，downsample 前一定也有，所以我們有了一個 $\text{rank}^-(A, v)$ 的 lower bound。

論文中有一個很巧妙的方法做了一個 upper bound。

首先寫一個新的 notation， $A_{v^-}$ 代表在 $A$ 裡比 $v$ 小的 elements。

對於每一個 $A\_{ij} \in \bar{A}\_{v^-}$ ，找它自己，它的左面，左下面和下面的 neighbor （當然，這些 neighbor 不一定 exists，不要便是了）。(就是 $A\_{i(j-1)}, A\_{(i+1)(j-1)}, A\_{(i+1),j}$)，把這個 set 叫做 $A\_{v^?}$

記得對於 ordered matrix，向左和向下是 monotonic increasing，所以這些 element 要不能比 $A\_{ij}$ 小，但 $A\_{ij}$ 本身比 $v$ 小，我們不知道這些 element 和 $v$ 誰大誰小，都有可能。

所以叫它 $A_{v^?}$ ，我們其實不知道這個 set 是不是比 $v$ 小。

我們看一個實際的例子, downsamples 前是這樣

```txt
   5   3   3   2
   6   4   4   3
   6   4   4   3
   8   6   6   5
```

downsample 後是這樣

```txt
5 3 2
6 4 3
8 6 5
```

假設 $v$ 是 5，標記在括號裡的是 $\bar{A}_{v^-}$

```txt
   5   3   (3)   (2)
   6   4    4     3
   6   4   (4)   (3)
   8   6    6     5
```

然後我們再找 $A_{v^?}$ 就找到這些

```txt
   5   (3)   (3)   (2)
   6   (4)   (4)   (3)
   6   (4)   (4)   (3)
   8   [6]   [6]   [5]
```

留意 $A_{v^?}$ 裡面既有比 $v$ 小的，但也有不比 $v$ 小的 （用方括號標記了)

重點是，沒有標記的，通通都不比 $v$ 小，換句話說， $A\_{v^?}$ 是 $A\_{v^-}$ 的 superset ⋯

為甚麼呢？除了右上角外，任何一個比 v 小的元素，它的右，上或者右上，必然比 $v$ 小，這三個總有一個是在 $\bar{A}\_{v^-}$ 裡的，所以它必然被包括在 $A\_{v^?}$ 裡。

右上角呢，假如右上角小於 $v$ ，右上角必然在 $\bar{A}\_{v^-}$ 裡，自然也必然在 $A\_{v^?}$ 裡。

所以 $A\_{v^?}$ 是 $A\_{v^-}$ 的 superset。

所以 $\text{rank}^-(A, v) = |A\_{v^-}|\le|A\_{v^?}|\le 4|\bar{A}\_{v^-}| = 4\text{rank}^-(\bar{A}, v)$

> 注：原文中 $A$ 的 subscript 是 $L$ ，這個很奇怪，我不能理解，所以改了用 $v^-$ 來代表這是比 $v$ 小的 subset ⋯

## Helper functions

因為 matrix 是 sorted，我們可以利用走樓梯的方式用 $O(n)$ 時間計一個 $n \times n$ matrix $A$ 裡面的一個 element $k$ 的 $\text{rank}^-(A, k)$

方法是在每一列（注意，台灣用法）找到比 $k$ 小的數值的位置。假設我們要找 $\text{rank}^-(A, 4)$

```txt
   5   (3)   3   2
   6    4   4   (3)
   6    4   4   (3)
   8    6   6    5
```

留意第四列沒有括號，它就是沒有。

留意括號不會在上一列括號的左方，這個是必然的嗎？

嗯，這個是必然的。

在上一列的括號是在第一行的情況不用考慮，根本不存在左手邊。

否則，上一列的括號的左手邊必然最小是 4。

下一列的左手邊最小也必然是 4。

然而我們要找的是比 4 小的位置，所以括號必然不在左邊。

有了這個想法，算法就很簡單了。

```txt
從第一行開始
每一列
  向右走直到找到比 4 小的 （注意，第二列的搜索由第一列結束的位置繼續
  如果有，標記它
  否則結束
```

這樣，你最多只能往右走 n - 1 次，所以是 $O(n)$ 。

當然，完全對稱的情況下，你也可以找到 $\text{rank}^+(A, k)$ ，不過是方向相反而已，我們嘗試找 $\text{rank}^+(A, 4)$

```txt
[5]  3   3   2
[6]  4   4   3
[6]  4   4   3
 8   6   6  [5]
```

這次是行下到上，從右到左，方法一樣。

有了這些括號後，要找 $\text{rank}^-$ 或 $\text{rank}^+$ 的值就很簡單了，只是把括號或以後的個數加起來而己

例如 $\text{rank}^-(A, 4) = 3 + 1 + 1 = 5$ ， $\text{rank}^+(A, 4) = 1 + 1 + 1 + 4 = 7$

最後，論文中有一個細節沒細講，假如我想找 $6 \gt  x \gt  3$ 的數值（留意 bounds 是 exclusive），要怎麼找呢？

首先用標記法找到括號，找 $\text{rank}^-(A, 6)$ 和 $\text{rank}^+(A, 3)$

```txt
[(5)]  3    3    2
  6   (4)  [4]   3
  6   (4)  [4]   3
  8    6    6  [(5)]
```

這樣答案就很明顯了，每一列從圓括號掃到方括號就好。

找 rank 的過程是 $O(n)$ ，掃的過程和 output 的 size 一樣，這個算法的速度 depends on output size，叫做 output sensitive algorithm。

當然，如果我們保證輸入時，輸出的個數必然是 $O(n)$ ，那麼這個算法的速度也是 $O(n)$ 。

## 戲肉 - Main function

我暫時還卡在不知道 $\overline{k\_1}$ 和 $\overline{k\_2}$ 的設計方法，這次我選擇 leap of faith，不管了，假設它確實有論文中說的 property 去理解 biselect。

我們想找 A 裡面的第 $k\_1$ ， $k\_2$ 個數值，我們知道 $\bar{A}$ 裡面的第 $\overline{k\_1}$, $\overline{k\_2}$ 個數值，而且論文說有這些 properties。

$\overline{A}[\overline{k\_1}] \ge A[k\_1] \ge A[k\_2] \ge \overline{A}[\overline{k\_2}]$ ，其中 $A[x]$ 代表 $A$ 的第 $x$ 個小的 element (從 1 開始)

而且 $k\_1 - k\_2 = O(n)$

那麼答案其實已經呼之欲出，利用上述 algorithm，找到 $\overline{k\_1} \gt  x \gt  \overline{k\_2}$ 的值，再用 QuickSelect 就好。

由於我們只保證 $\ge$ ，而上述 algorithm 只有 $\gt$ ，所以有可能 out of bounds，而要處理，這應該就是那些 if 處理。

只要跳過那個設計方法，明白這個算法不難。

## 實現

```py
import random

def partition(arr, low, high):
    chosen = random.randint(low, high)
    arr[chosen], arr[high] = arr[high], arr[chosen]
    pivot = arr[high]
    i = low - 1

    for j in range(low, high):
        if arr[j] <= pivot:
            i += 1
            arr[i], arr[j] = arr[j], arr[i]

    arr[i + 1], arr[high] = arr[high], arr[i + 1]
    return i + 1

def quick_select(arr, low, high, k):
    if low == high:
        return arr[low]

    pivot_index = partition(arr, low, high)

    if k == pivot_index:
        return arr[k]
    elif k < pivot_index:
        return quick_select(arr, low, pivot_index - 1, k)
    else:
        return quick_select(arr, pivot_index + 1, high, k)

def find_kth_smallest(arr, k):
    if k < 1 or k > len(arr):
        raise ValueError("Invalid k value")

    return quick_select(arr, 0, len(arr) - 1, k - 1)

def find_kth_smallest_matrix(matrix, k):
    n = len(matrix)
    l = n * n
    if k < 1 or k > n * n:
        raise ValueError("Invalid k value")
    else:
        index = list(range(n))
        return find_kth_smallest_matrix_helper(matrix, index, k, k)[0]

def find_kth_smallest_matrix_helper(matrix, index, k1, k2):
    n = len(index)
    if n <= 2:
        values = []
        for r in index:
            for c in index:
                values.append(matrix[r][c])
        values.sort()
        return (values[k1 - 1], values[k2 - 1])
    else:
        down_sampled_index = []
        for i in range(n//2):
            down_sampled_index.append(index[2 * i])
        if n % 2 == 0:
            bar_k1 = n + 1 + (k1 + 3) // 4         
        else:
            bar_k1 = (k1 + 2 * n + 4) // 4
        down_sampled_index.append(index[n - 1])
        bar_k2 = (k2 + 3) // 4
        (a, b) = find_kth_smallest_matrix_helper(matrix, down_sampled_index, bar_k1, bar_k2)
        (ra, rb, l) = ranks(matrix, index, a, b)
        if ra <= k1 - 1:
            x = a
        elif k1 + rb - n * n <= 0:
            x = b
        else:
            x = find_kth_smallest(l, k1 + rb - n * n)

        if ra <= k2 - 1:
            y = a
        elif k2 + rb - n * n <= 0:
            y = b
        else:
            y = find_kth_smallest(l, k2 + rb - n * n)

        return (x, y)

def lower_rank(matrix, index, value):
    n = len(index)
    c = 0
    answer = 0
    positions = []
    for r in range(n):
        while c < n and matrix[index[r]][index[c]] >= value:
            c += 1
        positions.append(c)
        answer += n - c
    return (answer, positions)

def upper_rank(matrix, index, value):
    n = len(index)
    c = n - 1
    answer = 0
    positions = []
    for r in reversed(range(n)):
        while c >= 0 and matrix[index[r]][index[c]] <= value:
            c -= 1
        positions.append(c)
        answer += c + 1
    positions.reverse()
    return (answer, positions)

def ranks(matrix, index, a, b):
    n = len(index)
    (ra, a_positions) = lower_rank(matrix, index, a)
    (rb, b_positions) = upper_rank(matrix, index, b)
    l = []
    for r in range(n):
        for c in range(a_positions[r], b_positions[r] + 1):
            l.append(matrix[index[r]][index[c]])
    return (ra, rb, l)

class Solution(object):
    def kthSmallest(self, matrix, k):
        for row in matrix:
            row.reverse()
        return find_kth_smallest_matrix(matrix, k)
        
```

這段代碼經過 LeetCode 測試和暴力測試找所有 elements，所以應該沒有 bug，可以放心使用。[^quickselect-duplicates]

> 使用別人的原代碼要 attribution 那是基本禮貌 ⋯ 無謂的 license 就不放了。Quick Select 實現在自於 chatgpt，至於它從哪裡抄回來的我也不知。

## 證明 Lemma 5.1

我忘了在哪見過這麼一句話

Just because you can implement an algorithm doesn't mean you understand it completely.

現在就是這個情況，跟著 paper 寫不難，難在不知道 $\overline{k\_1}$ 和 $\overline{k\_2}$ 的來源，總是不舒服。

希望接下來的第五段可以給予解答，很可惜第五段的 paper 的證明都很簡短 ⋯ 對於 author （或者 author 認為的讀者）都覺得簡單吧 ⋯ 其實也沒有很難，就是有很多細節要小心。

這裡是我補充 author 沒有寫的部份。

Lemma 5.1 補充

Equation 4.1 是

$$
\overline{k_1} = \begin{cases} n + 1 + \left\lceil\frac{k_1}{4}\right\rceil  &\quad\text{if n is even} \\
\left\lceil\frac{k_1 + 2n + 1}{4}\right\rceil &\quad\text{if n is odd}
\end{cases}
$$

Equation 4.2 是

$$
\overline{k_2} = \left\lfloor\frac{k_2 + 3}{4}\right\rfloor
$$

論文只寫了 it is easy to show by induction

$$
\begin{gathered}
\overline{n}^2 \ge \overline{k_1} \ge \overline{k_2} \ge 1 \\
\overline{k_1} - \overline{k_2} \le 4\overline{n} - 4
\end{gathered}
$$

這裡補充一下

先假設 $n$ 是 even

$$
\begin{aligned}
\overline{k_1} &= n + 1 +  \left\lceil\frac{k_1}{4}\right\rceil \\
\overline{k_2} &= \left\lfloor\frac{k_2 + 3}{4}\right\rfloor \\
\overline{n} &= \frac{n+2}{2}
\end{aligned}
$$

先把要求證的全部放進去試試，這個就是要求[^floor-ceil-transcription]

$$
\begin{gathered}
\frac{n^2 + 4n + 4}{4} \ge n + 1 + \left\lceil\frac{k_1}{4}\right\rceil \ge \left\lfloor\frac{k_2 + 3}{4}\right\rfloor \ge 1 \\
n + 1 + \left\lceil\frac{k_1}{4}\right\rceil - \left\lfloor\frac{k_2 + 3}{4}\right\rceil \le 4\left(\frac{n+2}{2}\right)  - 4
\end{gathered}
$$

留意因為 induction 所以可用

$$
\begin{gathered}
n^2 \ge k_1 \ge k_2 \ge 1 \\
k_1 - k_2 \le 4n - 4
\end{gathered}
$$

首先嘗試證 $\frac{n^2 + 4n + 4}{4} \ge n + 1 +\left\lceil\frac{k_1}{4}\right\rceil$

$$
\begin{aligned}
n^2 &\ge k_1 \\
\frac{n^2 + 4n + 4}{4} &\ge \frac{k_1 + 4n + 4}{4} \\
&\ge n + 1 + \frac{k_1}{4}
\end{aligned}
$$

臨門一腳，差個 ceil，怎麼辦呢？

我們不可以隨便加 ceil 進去，因為這樣做不一定 valid，例子：

$$
\begin{aligned}
10 &\ge 10 \\
\frac{10}{4} &\ge \frac{10}{4} \\
\frac{10}{4} &\ge \left\lceil\frac{10}{4}\right\rceil \quad[這步錯!這樣是 2.5 \ge 3]
\end{aligned}
$$

但是假如 $x$ 是一個 integer 而且 $x\ge y$ ，那麼我們就可以加 ceil 寫 $x \ge \lceil y \rceil$ 了。

因為 $n$ 是 even，左邊是 integer，所以證到第一個 $\frac{n^2 + 4n + 4}{4} \ge n + 1 + \left\lceil\frac{k_1}{4}\right\rceil$

第二個比較容易, ceil 總是變大，floor 總是變小，沒有上面的麻煩。

$$
\begin{aligned}
k_1 &\ge k_2 \\
4n + 4 + k_1 &\ge k_2 + 3 \\
n + 1 + \frac{k_1}{4} &\ge \frac{k_2 + 3}{4} \\
n + 1 + \left\lceil\frac{k_1}{4}\right\rceil &\ge \left\lfloor\frac{k_2 + 3}{4}\right\rfloor
\end{aligned}
$$

第三個

$$
\begin{aligned}
k_2 &\ge 1 \\
k_2 + 3 &\ge 4 \\
\frac{k_2 + 3}{4} &\ge 1 \\
\left\lfloor\frac{k_2 + 3}{4}\right\rfloor &\ge 1 \\
\end{aligned}
$$

這裡加 floor 的原理和證第一個的原理類似，利用這個事實

當 $y$ 是 integer， $x \ge y \implies \lfloor x \rfloor \ge y$

第四個[^even-bound-formula]

$$
\left\lceil\frac{k_1 + 2n + 1}{4}\right\rceil - \left\lfloor\frac{k_2 + 3}{4}\right\rfloor \le 4\left(\frac{n+2}{2}\right)  - 4
$$

又是討人厭的 ceil 和 floor，這次我們可以用這兩個簡單的 bound 做，顯然 $\lceil x \rceil \le x + 1$, $\lfloor y \rfloor \ge y - 1$

所以[^even-bound-algebra]

$$
\begin{aligned}
& n + 1 + \left\lceil\frac{k_1}{4}\right\rceil - \left\lfloor\frac{k_2 + 3}{4}\right\rfloor \\
&\le n + 1 + \left(\frac{k_1}{4} + 1\right) - \left(\frac{k_2 + 3}{4} - 1\right) \\
&=   \frac{4n + 4 + k_ 1 + 4 - k_2 - 3 - 4}{4} \\
&=   \frac{k_1 - k_2 + 4n - 3}{4} \\
&\le \frac{4n - 4 + 4n - 3}{4} \\
&\le \frac{8n - 7}{4} \\
&=   2n - \frac{7}{4} \\
&\le 2n \\
&= 4\overline{n} - 4
\end{aligned}
$$

even 的 case 好了，我們再假設 $n$ 是 odd

$$
\begin{aligned}
\overline{k_1} &=  \left\lceil\frac{k_1 + 2n + 1}{4}\right\rceil \\
\overline{k_2} &= \left\lfloor\frac{k_2 + 3}{4}\right\rfloor \\
\overline{n} &= \frac{n+1}{2}
\end{aligned}
$$

再把要求證的全部放進去試試，這個就是要求[^floor-ceil-transcription]

$$
\begin{gathered}
\frac{n^2 + 2n + 1}{4} \ge \left\lceil\frac{k_1 + 2n + 1}{4}\right\rceil \ge \left\lfloor\frac{k_2 + 3}{4}\right\rfloor \ge 1 \\
\left\lceil\frac{k_1 + 2n + 1}{4}\right\rceil - \left\lfloor\frac{k_2 + 3}{4}\right\rceil \le 4\left(\frac{n+1}{2}\right)  - 4
\end{gathered}
$$

留意因為 induction 所以可用

$$
\begin{gathered}
n^2 \ge k_1 \ge k_2 \ge 1 \\
k_1 - k_2 \le 4n - 4
\end{gathered}
$$

第一個

$$
\begin{aligned}
n^2 &\ge k_1 \\
n^2 + 2n + 1 &\ge k_1 + 2n + 1 \\
\frac{n^2 + 2n + 1}{4} &\ge \frac{k_1 + 2n + 1}{4} \\
\frac{n^2 + 2n + 1}{4} &\ge \left\lceil\frac{k_1 + 2n + 1}{4}\right\rceil
\end{aligned}
$$

第二個

$$
\begin{aligned}
k_1 &\ge k_2 \\
k_1 + 2n + 1 &\ge k_2 + 3 \\
\frac{k_1 + 2n + 1}{4} &\ge \frac{k_2 + 3}{4} \\
\left\lceil\frac{k_1 + 2n + 1}{4}\right\rceil &\ge \left\lfloor\frac{k_2 + 3}{4}\right\rfloor
\end{aligned}
$$

第三個和 even 的 case 一樣

第四個[^floor-ceil-transcription]

$$
\begin{aligned}
& \left\lceil\frac{k_1 + 2n + 1}{4}\right\rceil - \left\lfloor\frac{k_2 + 3}{4}\right\rceil \\
\le& \left(\frac{k_1 + 2n + 1}{4} + \frac{3}{4}\right) - \left(\frac{k_2 + 3}{4} - \frac{3}{4}\right) \\
=& \left(\frac{k_1 + 2n + 1 + 3 - k_2 - 3 + 3}{4}\right) \\
=& \left(\frac{k_1 - k_2 + 2n + 4}{4}\right) \\
\le& \left(\frac{4n - 4 + 2n + 4}{4}\right) \\
\le& \left(\frac{6n}{4}\right) \\
\le& \left(\frac{8n - 8}{4}\right) \quad{2n - 8 \ge 0}\\
=& 2n - 2 \\
=& 4\left(\frac{n + 1}{2}\right) - 4
\end{aligned}
$$

留意我需要用到 $2n - 8 \ge 0$ ，換句話說只有 $n \ge 4$ 這個證明才有效。但我們只有 $n \ge 3$ ，所以在 $n = 3$ 的情況下，我們還沒證明完。

證明 $n = 3$ 的情況只能說是簡單暴力，我們有 $k\_1 - k\_2 \le 4n - 4 \implies k\_1 \le k\_2 + 8$ ，接下來只要證 $\left\lceil\frac{k\_2 + 8 + 2(3) + 1}{4}\right\rceil - \left\lfloor\frac{k\_2 + 3}{4}\right\rceil \le 2(3) - 2$ 就好了。設 $k\_2 = 4d + r$, $0 \le r \lt  4$ 。[^floor-ceil-transcription]

$$
\begin{aligned}
&\left\lceil\frac{4d + r + 15}{4}\right\rceil - \left\lceil\frac{4d + r + 3}{4}\right\rceil \\
=& d + 3 + \left\lceil\frac{r + 3}{4}\right\rceil - d - \left\lfloor\frac{r + 3}{4}\right\rfloor \\
=& 3 + \left\lceil\frac{r + 3}{4}\right\rceil - \left\lfloor\frac{r + 3}{4}\right\rfloor \\
\le& 4
\end{aligned}
$$

最後一個結論當然是試 $r = 0, 1, 2, 3$ 了

## 證明 Theorem 5.2

Lemma 5.1 告訴我們一些關於 $\overline{k\_1}$ 和 $\overline{k\_2}$ 的 bounds，尤其是 $\overline{k\_1} - \overline{k\_2}$ 是 $O(n)$ ，但其實沒有告訴我們為甚麼它一定包含第 $k\_1$ 和第 $k\_2$ 個數值。

Even case

$$
\begin{aligned}
& \text{rank}^+(A, a) \\
\le& 4\overline{n}^2 - 4\overline{k_1} \\
=& 4\left(\frac{n+2}{2}\right)^2 - 4\left(n + 1 + \left\lceil\frac{k_1}{4}\right\rceil\right) \\
=& n^2 + 4n + 4 - (4n + 4 + 4\left\lceil\frac{k_1}{4}\right\rceil) \\
=& n^2 - 4\left\lceil\frac{k_1}{4}\right\rceil \\
\le& n^2 - k_1
\end{aligned}
$$

Odd case

$$
\begin{aligned}
& \text{rank}^+(A, a) \\
\le& 4\overline{n}^2 - 4\overline{k_1} \\
=& 4\left(\frac{n+1}{2}\right)^2 - 4\left(\left\lceil\frac{k_1 + 2n + 1}{4}\right\rceil\right) \\
=& n^2 + 2n + 1 - 4\left(\left\lceil\frac{k_1 + 2n + 1}{4}\right\rceil\right) \\
=& n^2 - 4\left(\left\lceil\frac{k_1 + 2n + 1}{4}\right\rceil - \frac{2n + 1}{4}\right) \\
\le& n^2 - k_1
\end{aligned}
$$

最後的一個 bound 是這樣看，如果 ceiling 裡的正好是 integer，那麼 $\le$ 其實就是 $=$ ，否則 () 只能比 $k_1$ 大。

$$
\begin{aligned}
& \text{rank}^-(A, b) \\
\le& 4\overline{k_2} - 4\\
=& 4\left(\left\lfloor\frac{k_2 + 3}{4}\right\rfloor\right) - 4 \\
\le& 4\left(\frac{k_2 + 3}{4}\right) - 4 \\
=& k_2 + 3 - 4 \\
=& k_2 - 1
\end{aligned}
$$

接下來的證明其實論文寫得很清楚，就不用重覆了。

## 讀後感

整個算法的理解，由發現這份論文開始，到寫完這篇筆記，總共花了差不多兩個星期的時間。

事實上，就是兩個周末，該上班還得上班，然後該玩玩。

對著論文實現算法其實不難，主要是證明，然後總要處理 ceil/floor 真的好煩 ⋯ 然後其實這些證明本身沒甚麼用，最多算是練習不等式。

奇怪的問題，我相信很可能沒有甚麼應用 ⋯ 當然，本來去 motivate 它的那個 VLSI use case 除外。

Cecilia,

25th, May, 2024

[^quickselect-duplicates]: **代碼錯誤及複雜度問題：** `partition` 把所有等於 pivot 的 entries 都放到同一側。若 array 有 `m` 個相同值，pivot 每次都落在最右端，找最小 rank 時只能每次排除一個 entry，即使 randomize pivot 仍然需要 `O(m^2)` work 和 `O(m)` recursion depth。單獨呼叫 `find_kth_smallest([1] * 1100, 1)` 在 Python 的預設 recursion limit 下會引發 `RecursionError`。所以這個 helper 並不對所有含重覆值的 inputs 保證 expected linear time；需要能一次處理 equal entries 的 three-way partition，亦要留意 recursion depth。這個反例針對 helper，不是宣稱上面的所有 matrix inputs 都會出錯。

[^even-bound-formula]: **勘誤：** 這裡仍在 even case，但 displayed formula 使用了 odd case 的 `bar_k1`。左邊應是 `n + 1 + ceil(k1/4) - floor((k2 + 3)/4)`；下一段展開的起點才是 even case 的式子。

[^even-bound-algebra]: **勘誤：** 減去 `((k2 + 3)/4 - 1)` 時，最後的 constant term 應是 `+4`，不是 `-4`。因此這一步展開的 numerator 應是 `k1 - k2 + 4n + 9`，不能推出原文的 `2n - 7/4`。最終目標仍可證明：由 `k1 - k2 <= 4n - 4` 得 `ceil(k1/4) - ceil(k2/4) <= n - 1`，再用 `floor((k2 + 3)/4) = ceil(k2/4)`，左邊便至多是 `n + 1 + n - 1 = 2n`。

[^floor-ceil-transcription]: **記號勘誤：** 標記的式子中，第二項的 `\left\lfloor` 應配 `\right\rfloor`，不是 `\right\rceil`；`n = 3` 的 displayed derivation 的第一行亦應減去 floor，而不是 ceil。這些地方應一致使用 `bar_k2 = floor((k2 + 3)/4)`。
