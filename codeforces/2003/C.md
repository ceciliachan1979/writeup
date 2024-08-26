# CodeForces contest 2003 (Div2) 第 C 題

問題係[呢度](https://codeforces.com/problemset/problem/2003/C)

記一次搞笑嘅故事，寫錯 code，但因禍得福 accept 咗。

個 condition 寫得好古怪，我哋睇 examples。

呢個 case 唔係 pleasant pair
```
aaa
i j
```

呢個 case 都唔係
```
aaabb
i   j
```

但呢個 case 可以

```
aabbbba
i     j
```

當然呢個都得
```
aabbbbc
i     j
```

個 pattern 係，如果你將條 string 通過係咪相同 character 嚟拆。超過兩件就係 pleasant pair，如果唔係就唔係。

正路咁諗，如果想要佢地愈多 pleasant pair 愈好，我哋會想將啲 pieces 做得愈細愈好，就寫咗呢段 code。

```py
from sys import stdin
from collections import Counter
from heapq import heappush, heappop

def run(s):
    hist = Counter(s)
    queue = []
    for k in hist:
        heappush(queue, (-hist[k], k))
    ans = []
    while len(queue) > 0:
        (nf, c) = heappop(queue)
        ans.append(c)
        f = -nf
        f -= 1
        if f > 0:
            heappush(queue, (-f, c))
    print("".join(ans))

def main():
    data = []
    for line in stdin:
        data.append(line.strip())
    num_test_case = int(data[0])
    line_number = 1
    for _ in range(num_test_case):
        n = data[line_number]
        line_number += 1
        s = data[line_number]
        line_number += 1
        run(s)
    
if __name__ == "__main__":
    main()
```

個 rough idea 係 greedily select frequency 最高嘅 emit，個 string 如果係 `aabb`，就會變成 `abab`。

諗深一層，如果我係想愈多件愈好，呢個 program 係有 bug 嘅，考慮 `aaabc`，我個 algorithm 會照出 `aaabc`，但其實 `abaca` 有五件，好過 `aaabc` 得三件。

呃，咁點 accept 嘅呢？

# Editorial proof

以下其實唔係我諗嘅，我只係 paraphrase 咗個 official editorial solution 同補充返佢冇寫嘅嘢。

首先，全個 string 有 $\frac{n(n-1)}{2}$ 個 pairs。
如果個 $i$, $j$ 係同一件，呢個係算 good pair。
如果個 $i$, $j$ 係連住嘅兩件，呢個唔算。
如果個 $i$, $j$ 係兩件而且冇連住，呢個係 pleasant pair，算 good pair。

所以其實 maximize good pair，就係要 minimize 連住嘅兩件。

如果你有 $m$ 件，每件個 size 係 $n_1, n_2, \cdots, n_m$，個 size 就會係 $n_1n_2 + n_2n_3 + \cdots + n_{m-1}n_{m}$

個 string 隨便你 reorder，然後 $m$ 係會變化，睇落依然好難 optimize。然後 editorial 題到其實個 optimal 係 $n-1$， $n$ 係條 string 嘅 length。

我哋首先證一個 lemma

如果得兩件，佢最少大過或等於 $n-1$

Proof： $n_1 (n - n_2)$ 係一條有 maximum 嘅 parabola，所以所有 possibility 都大過左右兩個端點。

然後 induction：

如果 $k$ 件都可以， $k + 1$ 件嘅時候

個 sum 都會係

$n_1 n_2 + n_2 n_3 + \cdots n_{k-1}n_{k} + n_{k}n_{k+1}$

Induction hypothesis 出

$n_1 n_2 + n_2 n_3 + \cdots n_{k-1}n_{k} \ge n - n_{k+1} - 1$

所以 $n_1 n_2 + n_2 n_3 + \cdots n_{k-1}n_{k} + n_{k}n_{k+1} \ge n - n_{k+1} - 1 + n_k + n_{k-1} - 1 \ge n - 1$ (留意 $n_k \ge 1$)

所以 $n-1$ 是一個 lower bound，但同時我哋有辦法 reach $n-1$。

我地又用 induction，如果得兩個 distinct characters，我哋儘量 `abab` 直到其中一個多咗就唯有用佢，睇吓會點？

前面 `abab` 個段每個 product 都係 1，我地可以咁睇

```
 1114
ababaaaa
```
用圖睇就唔難睇得出點解 `abab` 咁做可以達到 lower bound $n-1$。

如果我地有 $k$ 個 distinct characters 嘅時候可以做到 $n-1$，考慮 $k+1$ 個 characters

呢 $k+1$ 個 characters 裡面其實依然可以 `abcdabcd` 咁去做，直到其中一個(或多個同時) character 用晒為止，之後就 inductively 咁做。

我地又睇圖

```
 1111111 ?<-------->
abcdabcd|inductively
```

`<-->` 個段係 inductive hypothesis，我哋保證咗佢長度 ok。
前面啲 `1` 個原因都好明顯，by construction 佢哋全部係 `1 x 1`

個 `?` 會係要諗嘅位，個 term 會係 `?` (即係 inductively 第一件個長度再乘以 1，即係 `?` 個長度)
所以睇圖，依然係 `n-1`。

所以用呢個方法可以 reach 到 $n-1$，而 $n-1$ 又係 lower bound，即係 optimal。

我個 algorithm 其實係好彩，當我撞到 frequency 一樣嘅話，個 code 一樣會 output `abcabc` 咁，所以 work。
