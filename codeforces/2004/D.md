# CodeForces contest 2003 (Div2) 第 D 題

問題係[呢度](https://codeforces.com/problemset/problem/2004/D)

呢題 kick 咗我好多日，原因係我諗咗去一個錯誤嘅 direction。

條問題本身唔難，用 position做 node，有相同 color 做 edge，Dijkstra 就得。

難就難在 200,000 個 nodes，只有六種唔同嘅 color pairs，所以個 graph 應該 dense，再加上 200,000 個 queries ⋯

直爆 Dijkstra 肯定唔得。

諗到一個方向，geometrically，其實如果 RG(1) -> ... -> GB(2) -> ... -> GB(3)，其實你永遠唔需要考慮 RG(1) 去 GB(3)，咁樣可以 reduce 個 number of edges。

但如果個圖係咁

RG GB RG GB ....

個 graph search 一樣要行晒個 list﹐again 200,000 次行 200,000 次個 nodes 係會死。

跟住就諗到一個 idea，中間飛過去得唔得，左右兩邊搵晒最近嘅 nodes，每次 query 將左右 subgraph 連埋嚟 search，左右 closest node only 嘅 subgraph 應該係 constant size

但呢個 idea 其實係錯嘅！

```
GB RG ........ GB .......... BY GB
p  q           r             s  t
```

其中 `...` 全是 `RG`.

個 query 係 q 去 s，如果我用最近近 nodes 去 form 一個 subgraph 嘅話，q 可以去 p 再去 s，又或者 q 去 t 再去 s，但點都唔會搵到 `r`，因為 `r` 根本唔係 any closest nodes，但 `p -> q -> r` 最快。

原來，一直係度誤導嘅自己嘅係一開始個 Dijkstra idea，其實呢條根本唔駛諗 graph，只係用返 common sense。

我哋當啲 nodes 係地鐵站，有四條線，全部行同一條路。正路你只會諗向前行。

如果有一條線可以直達，直接搭就可以了，明顯 optimal。

否則﹐如果中途可以轉車 (i.e. `src < change < dst`)，中途轉就得，依然係 optimal，轉車係冇 distance 嘅

唯一一個難題係如果唔得，咁點呢？

去相反方向最近嘅轉車站，再直接去就得，相反方向嗰段要行兩次！

呢個可能唔在在，即係話完全冇轉車站，咁 output `-1`。

要 check 有冇可以中途轉車可以用 prefix sum。
計最近 position 可以用一前一後兩個 scan 搵。

咁就 AC 咗喇！

Code:

```py
from sys import stdin

def run(colors, queries):
    # print(colors)

    # Step 1: Come up with an encoding scheme for the colors
    # Canonicalize by sorting the color, each possible color pair has a code
    # and produce a matrix to check compatibility.
    # Rewrite the given color array using the code
    n = len(colors)
    options = "RGBY"
    options = "".join(sorted(list(options)))
    codes = []
    split = []
    for i in range(4):
        for j in range(i + 1, 4):
            codes.append("%s%s" % (options[i], options[j]))
            split.append((i, j))
    compat = []
    for i in range(6):
        compat.append([])
        for j in range(6):
            (a, b) = split[i]
            (c, d) = split[j]
            bit = (a == c) or (a == d) or (b == c) or (b == d)
            compat[-1].append(bit)
    colors = [codes.index("".join(sorted(list(color)))) for color in colors]

    # Step 2: Precompute, for each location `pos``, for each color `c`
    # 1.) The number of nodes with color `c` on or to the left for `pos`
    # 2.) The closest node with color `c` on or to the left for `pos`
    # 3.) The closest node with color `c` on or to the right for `pos`

    # counts[i][j] is the number of nodes with color i before element j
    counts = [[0 for _ in range(1)] for _ in range(6)]

    # left[i][j] is the position of the rightmost node of color i before element j
    lefts  = [[-1 for _ in range(1)] for _ in range(6)]

    # right[i][j] is the position of the leftmost node of color i on or after element j
    rights = [[-1 for _ in range(1)] for _ in range(6)]

    for (pos, color) in enumerate(colors):
        for i in range(6):
            counts[i].append(counts[i][-1] + (1 if color == i else 0))
            lefts[i].append(pos if color == i else lefts[i][-1])

    for (pos, color) in [(t, colors[t]) for t in reversed(range(n))]:
        for i in range(6):
            rights[i].append(pos if color == i else rights[i][-1])

    for i in range(6):
        rights[i].reverse()

    # Step 3: Compute the closest compatible (but not same color) node for each position
    closest = []

    for (pos, color) in enumerate(colors):
        best = -1
        for i in range(6):
            if compat[i][color] and i != color:
                # print("Considering ", codes[i], " at position ", pos)
                # print("On the left, it is at ", lefts[i][pos])
                # print("On the right, it is at ", rights[i][pos + 1])
                left = lefts[i][pos]
                right = rights[i][pos + 1]
                if left != -1:
                    leftDist = pos - left
                    if best == -1 or leftDist < best:
                        best = leftDist
                if right != -1:
                    rightDist = right - pos
                    if best == -1 or rightDist < best:
                        best = rightDist
        closest.append(best)


    for (src, dst) in queries:
        if dst < src:
            (src, dst) = (dst, src)
        (src, dst) = (src - 1, dst - 1)
        srcColor = colors[src]
        dstColor = colors[dst]
        # print("(%s:%s) - (%s:%s)" % (src, srcColor, dst, dstColor))

        if compat[srcColor][dstColor]:
            # Case 1: If srcColor and dstColor are compat, just go directly
            print(dst - src)
        else:
            found = False
            for i in range(6):
                if i != srcColor and i != dstColor:
                    # print("There are ", counts[i][src + 1], " of color ", codes[i], " before ", src)
                    # print("There are ", counts[i][dst + 1], " of color ", codes[i], " before ", dst)
                    if counts[i][src + 1] != counts[i][dst + 1]:
                        found = True
                        break
            if found:
                # Case 2: There is a compat exchange between src and dst
                print(dst - src)
            else:
                # Case 3: There isn't a compat exchange between:
                best = -1
                if closest[src] != -1:
                    best = closest[src]
                if closest[dst] != -1:
                    if best == -1 or closest[dst] < best:
                        best = closest[dst]
                if best == -1:
                    # Case 3a: There isn't a compat exchange at all
                    print(-1)
                else:
                    # Case 3b: There is a compat exchange outside range
                    print(best * 2 + dst - src)

def main():
    data = []
    for line in stdin:
        data.append(line.strip())
    num_test_case = int(data[0])
    line_number = 1
    for _ in range(num_test_case):
        n, q  = [int(x) for x in data[line_number].split()]
        line_number += 1
        colors = data[line_number].split()
        line_number += 1
        queries = []
        for _ in range(q):
            src, dst  = [int(x) for x in data[line_number].split()]
            line_number += 1
            queries.append((src, dst))
        run(colors, queries)

if __name__ == "__main__":
    main()
```