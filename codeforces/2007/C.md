# CodeForces contest 2007 (Div2) 第 C 題

問題係[呢度](https://codeforces.com/contest/2007/problem/C)

呢題依然係講緊 invariant 心法。這題只有兩個 operations，就係 $+a$ 同 $+b$。

呃， $+a$ 同 $+b$ 有咩唔變？

設 $\gcd(a,b)=k$， $+a$ 即係 $+mk$， $+b$ 即係 $+nk$，即係 $\mod k$ 不變。

照上兩題係思路，你會想諗有冇辦法 reduce 晒啲 array entry 去 $\mod k$。但暫時嚟講係唔知點做，因為你淨係可以 $+a$ 同 $+b$。

呢個時候我哋可以用 Bezout's identity，條式係 $pa+qb = k$，雖然 $p$ 同 $q$ 都係整數，但係 $p$ 或者 $q$ 可能係負整數。

要解決負數呢個問題，我哋係做唔到 $-b$ 嘅，但可以將所有其它 elements 都 $+b$ 。咁做嘅話，對個 range 嚟講個效果係一樣嘅。咁就可以隨便 $+k$ 或者 $-k$，嚟做到一齊 $\mod k$ 嘅效果。

呢個位有一個伏，如果 $k=7$ ，個 array 變成咗 `[1,6]`，個 minimal range 唔係 5，因為你可以再變成 `[8,6]` 而做到 `2`。

要做到 minimal，想像啲點係個圈圈度，搵個包晒所有點最短嘅 arc，其實亦即係最長嘅 arc 嘅另一邊。

```py
from sys import stdin
from math import gcd

def run(arr, a, b):    
    n = len(arr)
    g = gcd(a, b)
    mod = [i % g for i in arr]
    mod.sort()
    if len(mod) == 1:
        print(0)
    else:
        best = mod[-1] - mod[0]
        for i in range(n):
            best = min(g - mod[i] + mod[i-1], best)
        print(best)

def main():
    data = []
    for line in stdin:
        data.append(line.strip())
    num_test_case = int(data[0])
    line_number = 1
    for _ in range(num_test_case):
        n, a, b  = [int(x) for x in data[line_number].split()]
        line_number += 1
        arr = [int(x) for x in data[line_number].split()]
        line_number += 1
        run(arr, a, b)

if __name__ == "__main__":
    main()
```