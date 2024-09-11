# CodeForces contest 1986 (Div3) 第 E 題

問題係[呢度](https://codeforces.com/contest/1986/problem/E)

呢題依然係講緊 invariant 心法。這題只有一個 operation，就係 $+k$。

今次個 invariant 係 $\mod k$ 一樣，呢點係理所當然嘅。

要砌成個 palindrome，對應嘅 pair 係一樣，所以佢哋 $\mod k$ 都一樣。

用呢點就可以 trim 咗好多 possibilities，我哋可以先根據 $\mod k$ 分組，因為只有同一組裡面嘅數字有可能一樣。

跟住 focus 一個 group，個 group 裡面個個 element 都係 $mk + r$，個個 element 個 $m$ 唔同，但 $r$ 一樣，所以可以只考慮 $m$。

每次 $+k$ 其實只係 increment $m$，所以其實問題係問最少要 increment 幾次先至可以將啲數字變成一對對。

自然地，你會諗到 sorting 呢個 greedy approach，我冇理由唔做 $m$ value 最接近嗰啲啫。

For even length array，sorting 係 optimal。

反證法：如果最細 $a$ 唔係 pair with 第二細 $b$，個兩 pairs 會係 $(a,c)$ 同埋 $(b,d)$

$(a,c)$ 要用 $c-a$ 次先整到 $(c,c)$
$(b,d)$ 要用 $d-b$ 次先整到 $(b,d)$

所以我想證 $(c-a)+(d-b)\ge(b-a)+|c-d|$，後面用 absolute 係因為我唔知 $c$ 大定 $d$ 大。

執一執就會變成 $c+d-|c-d|\ge 2b$。

- Case 1: $c \ge d$，明顯 $c+d-(c-d) = 2d \ge 2b$
- Case 2: $c \le d$，明顯 $c+d-(d-c) = 2c \ge 2b$

咁就證到 sorting 冇問題。

Odd length array 嘅話呢個 argument 就唔 work，因為最細可以唔 pair with anything。

要做 odd length array，其實就係揀邊一個唔要。Sort 完之後，可以做 prefix sum 同 suffix sum，計完之後就可以好快 evaluate 到 drop 一個嘅 optimal 係咩。試晒所有就可以了。

最後留意你最多只可能有 1 個 odd length array，因為 palindrome 只有一個 center。

```py
from sys import stdin

def run(arr, k):
    buckets = {}
    arr.sort()
    for v in arr:
        bucket = v % k
        if bucket not in buckets:
            buckets[bucket] = []
        buckets[bucket].append(v // k)
    odd = 0
    cost = 0
    for b in buckets:
        bucket = buckets[b]
        n = len(bucket)
        if n % 2 == 1:
            odd += 1
            if odd > 1:
                print(-1)
                return
    for b in buckets:
        bucket = buckets[b]
        n = len(bucket)
        if n % 2 == 1:
            # print("odd bucket: ", bucket)
            forward = []
            backward = []
            for i in range(n // 2):
                forward.append(bucket[2 * i + 1] - bucket[2 * i])
                backward.append(bucket[n - (2 * i + 1)] - bucket[n - (2 * i + 2)])
            backward.reverse()
            if n > 1:
                temp = sum(backward)
                best = temp
                for i in range(n // 2):
                    temp -= backward[i]
                    temp += forward[i]
                    if temp < best:
                        best = temp
                cost += best
        else:
            # print("even bucket: ", bucket)
            for i in range(n // 2):
                cost += bucket[2 * i + 1] - bucket[2 * i]
    print(cost)

def main():
    data = []
    for line in stdin:
        data.append(line.strip())
    num_test_cases = int(data[0])
    r = 1
    for test_case_id in range(num_test_cases):
        sizes = [int(x) for x in data[r].split()]
        r += 1
        k = sizes[1]
        arr = [int(x) for x in data[r].split()]
        r += 1
        run(arr, k)

if __name__ == "__main__":
    main()
```