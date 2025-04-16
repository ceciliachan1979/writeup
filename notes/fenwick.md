# Fenwick Tree

每一次使用 Fenwick Tree，都覺得這個 data structure 很神奇。又簡短，又好用。但每一次都忘記它是怎麼運作的。寫這一篇複習的目的，就是希望以後可以很快的看一看這篇筆記，然後就可以回憶起來了。

## left

在講 Fenwick Tree 之前，先介紹一個它用到的操作，`x & -x`。

我們先假設 `x` 的兩進位表示是 `abcd1000`，其中 `abcd` 是任意的 bit。

`-x` 是 `x` 的 2s complement，所以 `-x` 是 `ABCD1000`，其中 `ABCD` 是 `abcd` 的反轉。

然後取 `&`，就會得到 `00001000`，也就是 `x` 最右邊的 1 的位置。這個操作可以用來找出 `x` 最右邊的 1 的位置。

那麼 `x - (x & -x)` 就是把 `x` 最右邊的 `1` 的位置變成 `0`，其他的 bit 不變。我們把這個操作叫做 `left`，也就是 `left(x)`，之後我會解釋這個命名的由來。

## Decomposition

有了這個 left 操作，我們就可以講 prefix sum 的 decomposition 了。在 Fenwick tree 的 convention 中，array 永遠由 1 開始。然後我們利用這個符號 `[n, k)` 代表 `arr[k+1] + arr[k+2] + ... + arr[n]`，也就是從 `k+1` 開始到 `n` 的 prefix sum。

在 Fenwick tree 中，prefix sum 是通過 `left` 來做 decomposition 的。

比如說 `[21, 0)` 的 decomposition 是 `[21, 20) + [20, 16) + [16, 0)`，用兩進位表示可以很清楚的看到 `left` 的意義： 

```
left(21) = 20 .... left(10101) = 10100
left(20) = 16 .... left(10100) = 10000
left(16) = 0  .... left(10000) = 00000
```

留意只要一旦定義了 partial sum 的左手邊為 `k`，那麼它的右手邊就必然是 `left(k)`，所以我們可以用一個 array （名叫 `sum`) 去 store 這些 partial sum 的值。

```
[21, 0) = [21, 20) + [20, 16) + [16, 0)
        = sum[21] + sum[20] + sum[16]
```

這樣我們就可以用一個簡單的 loop 來計算 prefix sum 了：

```python
def left(i):
    return i - (i & -i)

def prefix_sum(i):
    s = 0
    while i > 0:
        s += sum[i]
        i = left(i)
    return s
```

前題當然是我們有 `sum` 的值了。那我們要怎麼計算 `sum` 呢？

有一個 trivial case，假如整個 array 都是 `0`，那麼 `sum` 當然也是 `0`。這個就是我們的 base case。

## Visualization

通過一個簡單的 visualization，我們可以更清楚的看到 Fenwick tree 的運作。

```
                   1 2 3 4 5 6 7 8 9 <- input array index
1                  x   
2                  x x 
3                      x 
4                  x x x x
5                          x
6                          x x
7                              x
8                  x x x x x x x x 
9                                  x
^
|
sum array index
```

這個圖是這樣看的，我們看看 `sum array index = 6` 的那一個 row，它在 input array index 中 的 `5` 和 `6` 有一個 `x`，這個表示 `sum(6) = arr(5) + arr(6)`

所以假如我們要計算 `[7, 0)` 的話，我們就看圖，首先 `sum[7]`，然後 `left(7)` 是 `6`，所以我們再加上 `sum[6]`，然後 `left(6)` 是 `4`，所以我們再加上 `sum[4]`，然後 `left(4)` 是 `0`，所以我們就結束了。這樣就可以計算出 prefix sum 了。整個方法就在這個圖上向左走，這就是為甚麼我把這個操作叫做 `left` 的原因。

## Update

現在來到最重要的一步了。之前我們只會把 `sum` 在 `array` 全 `0` 的情況下初始化為全 `0`。那麼非零的情況下，我們要怎麼更新 `sum` 呢？

我們假設 `arr[3]` 的值從 `0` 變成了 `5`，這個時候所有包含 `arr[3]` 的 partial sum 都要更新。

這個時候，這個 visualization 就好用了，在 3 的位置拉一條垂直線，就可以找到所有應該 update 的 partial sum 了。

```
                   1 2 3 4 5 6 7 8 9 <- input array index
1                  x   |
2                  x x |
3                      * 
4                  x x * x
5                      |   x
6                      |   x x
7                      |       x
8                  x x * x x x x x 
9                      |           x
^
|
sum array index
```

應該 update 的位置都用 `*` 標示了，分別是 `sum[3]`、`sum[4]` 和 `sum[8]`。

畫好了以為用眼看當然容易，但用代碼如何實現呢？這就是 Fenwick tree 的 update 操作了。

## 必要條件

假設我們要 update `arr[p]`，`sum[t]` 會包含 `p` 嗎？我們需要這個條件：

```
left(t) < p <= t
```

有趣的是，只要我們決定了最右邊的 `1` 在甚麼位置，`t` 其實最多只有一個選擇。

假如我們選擇這個是 `t` 的最後一個 `1` bit。

```
left(t) = xyz000
      p = abcdef
      t = xyz100
```

這個時候要符合 `left(t) < p <= t`，我們可以看出：

```
xyz000 < abcdef  => (xyz < abc) || (xyz == abc && 000 < def)
abcdef <= xyz100 => (abc == xyz) && (def <= 100) || abc < xyz
```

綜合一下就發現只能是 `(abc == xyz) && (000 < def <= 100)`

換句話說，我只要決定了哪一個 bit，基本上 `t` 就確定了，而成不成就看 `000 < def <= 100` 是否成立。

## 具體例子

假設 `p` 是 `101100`

- `t` 是 `101101` 是不成的，這個時候 `def -> 0`，而我們要求 `0 < def <= 1`。 (Case 1)
- `t` 是 `101110` 是不成的，這個時候 `def -> 00`，而我們要求 `00 < def <= 10`。 (Case 1)
- `t` 是 `101100` 是可以的，這個時候 `def -> 100`，而我們要求 `000 < def <= 100`。 (Case 2)
- `t` 是 `101000` 是不成的，這個時候 `def -> 1100`，而我們要求 `0000 < def <= 1000`。  (Case 3)
- `t` 是 `110000` 是可以的，這個時候 `def -> 01100`，而我們要求 `00000 < def <= 10000`。  (Case 4)

這樣就可以看出來 pattern

- Case 1: 本來最右手邊的 `1` 右面的 `0` 位是不成的 ⋯ 因為 `def` 會是 `0`
- Case 2: 本來最右手邊的 `1` 是可以的。
- Case 3: 本來最右手邊的 `1` 左面的 `1` 位是不成的，因為 `def` 會比 `100` 大。
- Case 4: 本來最右手邊的 `1` 左面的 `0` 位是可以的，因為 `def` 會比 `100` 小而又比 `000` 大。

又換句話說，其實就是由最右邊的 `1` 開始，往左走找 `0`。

## 超方便公式
由 `t = p` 開始，`x + (x & -x)` 就正好是下一個 `t`。

還記得 `(x & -x)` 就是最右的 `1` 的位置。

加這個數可以正好讓進位找到下一個左邊的 `0` 變成 `1`，完全就是我們想要的結果！

```
 101100
+000100
-------
 110000
```

所以 update 的代碼就是：

```python
def down(i):
    return i + (i & -i)

def update(i, delta):
    while i < len(sum):
        sum[i] += delta
        i = down(i)
```

看圖的話，我們可以說 `i + (i & -i)` 就是 `down(i)` 操作，因為它就是往下找下一個 `x` 的 row。

我常常誤以為 `down` 是 `left` 的反操作，其實不是。

```py
>>> def down(i):
...     return i + (i & -i)
... 
>>> def left(i):
...     return i - (i & -i)
... 
>>> left(down(20))
16
>>> down(left(20))
32
>>> 
```

把它改名成 `left` 和 `down` 會更好的說明這個操作並不是反操作，而是向不同方向走 ⋯
