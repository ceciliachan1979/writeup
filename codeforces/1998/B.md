# CodeForces contest 1999 (Div4) 第 B 題

問題係[呢度](https://codeforces.com/contest/1998/problem/B)

呢題幾乎用咗成個鐘，難點在於你睇唔睇到呢個重點

如果個 q 係 p rotate 一次會點？

如果某個 原 subarray 唔包括 $n$，咁所有 element 都會加 1，subarray sum 唔一樣

如果某個 原 subarray 包括 $n$，咁個 subarrray sum 會減咗 $n - 1$，要加返 $n-1$ 次 1 先得。

但咁即係全個 array，全個 array 係一定一樣的，所以 rotate 一次達到咗最少。

呢個 reasoning 一啲難度都冇 ⋯ 個難點係你諗唔諗到用 rotate 一次嚟試。

```py
from sys import stdin

def run(p):
    n = len(p)
    print(" ".join([str(v % n + 1) for v in p]))

def main():
    data = []
    for line in stdin:
        data.append(line.strip())
    num_test_case = int(data[0])
    line_number = 1
    for _ in range(num_test_case):
        n = data[line_number]
        line_number += 1
        p  = [int(x) for x in data[line_number].split()]
        line_number += 1
        run(p)

if __name__ == "__main__":
    main()
```