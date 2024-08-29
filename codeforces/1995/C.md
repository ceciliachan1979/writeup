# CodeForces contest 1995 (Div4) 第 C 題

問題係[呢度](https://codeforces.com/contest/1995/problem/C)

詭異的 floating point。

- 可以唔用，儘量唔好用 floating point。
- 可以唔用，儘量唔好用 floating point。
- 可以唔用，儘量唔好用 floating point。

重要嘅事情講三次

因為 floating point 真係好易出錯。

話說呢題其實有一個好簡單嘅 greedy algorithm，由左至右 square 上去便是了，呢個方法當然唔 work：

- overflow
- 就算係 Python，極大嘅數字都會 TLE

Input 係 `2 2 2 ... 2` 就會爆，`2 4 16 ...` 係升得好快的。

呢題嘅其中一種方法係 `log`，但其實 `log` 咗一樣爆

`log(2) 2log(2) 4log(2) 8log(2)`

一樣係 exponentially increasing，唯有再 `log` 一次。

`log(log(2)) log(2 log(2)) = log(2) + log(log(2)) log(4 log 2) = 2log(2) + log(log(2)) ...`

依家 linear 了。 

當然 `log(0)` 係唔存在，所以 `log(log(1))` 係唔得，要 handle 晒啲 1 先。

所以答案係咁

```py
from sys import stdin
from math import log, ceil

def run(arr):
    arr.reverse()
    while len(arr) > 0 and arr[-1] == 1:
        arr.pop()
    if len(arr) == 0:
        print(0)
    elif 1 in arr:
        print(-1)
    else:
        arr.reverse()
        arr = [log(log(n)) for n in arr]
        n = len(arr)
        ans = 0
        for i in range(1, n):
            if arr[i] < arr[i - 1]:
                m = ceil((arr[i - 1] - arr[i])/log(2))
                ans += m
                x = m * log(2)
                arr[i] += x
        print(ans)

def main():
    data = []
    for line in stdin:
        data.append(line.strip())
    num_test_case = int(data[0])
    r = 1
    for _ in range(num_test_case):
        count = data[r]
        r += 1
        arr = [int(x) for x in data[r].split()]
        r += 1
        run(arr)

if __name__ == "__main__":
    main()
```

不過好可惜，呢個答案係 wrong answer，某個 unknown input 出咗錯，偷睇 official 答案係咁：

```py
from sys import stdin
from math import log, ceil

def run(arr):
    arr.reverse()
    while len(arr) > 0 and arr[-1] == 1:
        arr.pop()
    if len(arr) == 0:
        print(0)
    elif 1 in arr:
        print(-1)
    else:
        arr.reverse()
        arr = [log(log(n)) for n in arr]
        n = len(arr)
        ans = 0
        for i in range(1, n):
            need = arr[i - 1] - arr[i]
            if need > 1e-9:
                m = ceil((need - 1e-9)/log(2))
                ans += m
                x = m * log(2)
                arr[i] += x
        print(ans)

def main():
    data = []
    for line in stdin:
        data.append(line.strip())
    num_test_case = int(data[0])
    r = 1
    for _ in range(num_test_case):
        count = data[r]
        r += 1
        arr = [int(x) for x in data[r].split()]
        r += 1
        run(arr)


if __name__ == "__main__":
    main()
```

留意個 `1e-9` 係重點 ⋯ 經過 debugging，發現在進行測試 `12 4 10 3 12 12 6` 時，其中一個 `(arr[5] - arr[4])/log(2)` 的數值是 `2.0000000000000004`，這個明顯是 2，但 `ceil` 會變 3，然後就悲劇了。

可以唔用，儘量唔好用 floating point，講第四次！