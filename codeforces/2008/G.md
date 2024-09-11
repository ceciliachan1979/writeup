# CodeForces contest 2008 (Div3) 第 G 題

問題係[呢度](https://codeforces.com/contest/2008/problem/G)

呢題依然係講緊 invariant 心法。金翅大鑊了，有好多 operations 添，點算呢？

個 idea 依然係 gcd，只不過係所有 array entries 嘅 gcd $g$。

因為今次有減號，所以可以直接整到 $g$。

方法係咁，求其執兩個唔一樣嘅 value，一直做大等於大減細，睇吓個 execution sequence？冇錯，結果就係做左 Euclidean algorithm，所以出到 $g$，當然，亦都出到 $0$。

我哋想要 maxmize mex，自然想啲數愈細愈好，但重覆冇意義，所以你會想做 $0,g,2g,\cdots,(n-1)g$ 咁樣。

咁出咗呢個 list 之後點呢？之後就簡單了， $[0, g)$ 呢 $g$ 個 numbers 只有一個 number 係度， $[g,2g)$ 又係，一直到 $[(n-1)g,g)$ 都係。

可以想像上依家要上堂點名。你有 $k-1$ 個後備係度，老師嗌到一個唔係度嘅人嘅時候就用後後備，咁叫到做一個冇後備嘅人用嘅人就係答案了。

因為有 $k-1$ 個人用，所以 $f =\min\left(\left\lfloor\frac{k-1}{g-1}\right\rfloor, n\right)$ 個 fully present blocks。

到呢個位下一個要點嘅號係 $fg$﹐如果 $f < n$ ，咁其實 $fg$ 係有到，所以下一個需要係後備嘅 $fg+1$，之後 $g-1$ 個人都係冇到。

然後你已經用咗 $f(g-1)$ 個後備，所以淨返 $k - f(g-1)$ 個人，然後呢個數一定係 $<g-1$ ，所以順序用晒啲後備佢，下一個依然冇到，答案就係佢了。

小心兩個 special case，如果個 array 只有一個 element，係 trivially 做到 $g$ ，但係做唔到 $0$ 。另外如果 $g=1$ ，個分母會係 $0$ ，實際上只係頭 $n$ 個 numbers 都唔係 absent，而冇用到任何後備。

```py
from sys import stdin
from math import gcd

def run(n, k, arr):
    n = len(arr)
    g = arr[0]
    for v in arr[1:]:
        g = gcd(g, v)
    if n == 1:
        print((k - 1) if k <= g else k)
    elif g == 1:
        print(k + n - 1)
    else:
        required = k - 1
        num_full_blocks = min((k - 1) // (g - 1), n)
        position = num_full_blocks * g
        if num_full_blocks < n:
            position += 1
        excluded = num_full_blocks * (g - 1)
        required -= excluded
        print(position + required)

def main():
    data = []
    for line in stdin:
        data.append(line.strip())
    num_test_case = int(data[0])
    line_number = 1
    for _ in range(num_test_case):
        n, k  = [int(x) for x in data[line_number].split()]
        line_number += 1
        arr = [int(x) for x in data[line_number].split()]
        line_number += 1
        run(n, k, arr)

if __name__ == "__main__":
    main()
```