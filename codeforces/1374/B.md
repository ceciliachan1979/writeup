# CodeForces contest 1374 (Div3) 第 B 題

問題係[呢度](https://codeforces.com/contest/1374/problem/B)

呢個係一連四題講 invariant 心法。呢啲問題都有一個特色。

你可以做某啲 operations，做完呢啲 operations 之後達到一個目標。然後通常會問步數，有或者 optimal 嘅一啲 property。

當然，題目條條都唔一樣，但係就有一個共通嘅諗法，就係我想講嘅 invariant。

究竟做呢啲 operations 嘅時候，有乜嘢係唔變嘅呢？

以呢條為例，你可以乘 $2$，你又可以整除 $6$，個數值當然會變，但係個 prime factorization 只有 $2$ 同埋 $3$ 嘅 power 會變，其它 prime factor 嘅 power 係唔會變。

咁就可以諗 $5$ 係冇可能，因為點整個 $5^1$ 個 $1$ 都係唔會變，即係點都冇可能變成 $1$。

又即係話可能變成 $1$ 嘅，只有 $2^x3^y$ 呢啲 form 嘅 number 先有可能。

將 $n = 2^x3^y$ map 去 $(x,y)$，可以睇到 $\times 2$ 對應嘅係 $(x+1,y)$，而 $\div 6$ 對應嘅係 $(x-1,y-1)$，當然任何時候 power 都 $\ge 0$。

變成一個 2D 問題就容易了，無非就係用 $(y-x)$ 次 $\times 2$  次推到 $x=y$，然後用 $y$ 次 $\div 6$ 推到 $(0,0)$，呢個方法當然要 $y \ge x$ 先可能做到。

如果 $y < x$ 呢？諗吓就知其實係冇得搞嘅，因為 $y < x$ 其實都係一個 invariant。

所以答案只係咁，factorize $n$，如果 $n = 2^x3^y$ 而且 $y \ge x$ ，答案就係 $(y - x) + y$，否則答案就係 $-1$

```py
from sys import stdin

def run(n):
    x = 0
    y = 0
    while n % 2 == 0:
        n = n // 2
        x += 1
    while n % 3 == 0:
        n = n // 3
        y += 1
    if n != 1:
        print(-1)
    elif x > y:
        print(-1)
    else:
        print(2 * y - x)
        
def main():
    data = []
    for line in stdin:
        data.append(line.strip())
    num_test_case = int(data[0])
    r = 1
    for _ in range(num_test_case):
        n = int(data[r])
        r += 1
        run(n)

if __name__ == "__main__":
    main()
```