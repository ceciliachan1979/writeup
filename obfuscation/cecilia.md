# Cecilia

Cecilia 是我的第一個 Obfuscation 作品

原題目非常簡單，只是寫一個程序找 1 .. 10 的算術平均值。

當然，我的代碼要隐藏這個意圖。

而且﹐為了噁心我的讀者 (因為他很有可能直接抄了當答案交)﹐我決定把我自己的名字用一個他不好改的方法寫進去 ⋯

```c++
#include <stdio.h>

int main()
{
    int c = 0;
    int e = 1;
    int l = 2;
    int i = 1;
    int a = 0;

    do
    {
        c = 2 * e + l;
        e = l;
        c = c % 11;
        i = i + l;
        l = c;
        i = i - c;
        a = a + c;
    }
    while (i != 1);

    float cecilia = a / 10.0;
    printf("%f\n", cecilia);
    return 0;
}
```
[Try it online!](https://tio.run/##XVBND4IwDL3vV1QMCaig44r6S7wsY0iTOQxCPBh@@1wZ8mEPXd5rX/s6mUgtzN3aLRqpu0LB@dUWWKfVlTE0LTwEmihmHwYuiJBwgVM@QeUgn6F2MJshrqvCawdc1MPjB1PQ4Ax2buIedD7RtGABqUtCCJzPHG3BtYp8yP@OZEmRF@FEI9UP@V2hVhAhbJzveHRa6lq4u5VEjV51BH5Kx094Nu6yMgrC8maCw68t9sVGtV1j6Obe2i8 "C (clang) – Try It Online")

嘿嘿 ⋯ 你怎麼知道這段代碼到底在做甚麼呢？

## 解說

如果我們只關注 `c`, `e` 和 `l` 的話，而且暫時先忘了 `c = c % 11` 這一句，我們就會發現在段代碼是在做重覆地做這個操作。

```c++
c = 2 * e + l
e = l
l = c
```

其它語句和這三個值無關，所以暫時可以忽略。

換句話說，我們可以把 `e, l, c` 想成是一個 sequence `a[k-2], a[k-1], a[k]`，只是第一句算出最後一個 term，然後 `k` 向前 shift。

```
a[k] = 2 * a[k - 2] + a[k - 1]
```

加上初始化的 `e = 1` 和 `l = 2`，不難試出這個 sequence 是 `a[k] = 2^(k+1)`。

所以 `c` 的值會是 `2^(k+1) % 11`。

`2` 是一個 primitive root mod `11`，所以 `c` 會走遍 `1..10`，而 `a` 就會把它們全加起來，最後得到 `1 + ... + 10 = 55` 這個結果，而 `/10.0` 就很明顯了。

## 設計

解釋完原理之後，就可以講講設計了。

這段代碼自然是設計出來的，並不是亂寫一堆代碼再解 recurrence relation。

換句話說，要如何設計一個 recurrence relation 隨心所欲的讓它輸出我想要的數字呢？

我在[這一篇文章](https://github.com/ceciliachan1979/math/blob/main/Misc/Obfuscate/Obfuscate.tex)中就有寫如何設計，其大意就是先解一些一般題，找到它的通解，再利用 initial condition 去做出我想要的樣子。

這段代碼第一次在 [Quora](https://www.quora.com/What-is-the-algorithm-to-find-the-average-of-the-numbers-1-to-10/answer/Cecilia-Chen-45) 發佈。