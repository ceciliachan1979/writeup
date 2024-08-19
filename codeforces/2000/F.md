# CodeForces contest 2000 (Div3) 第 F 題

問題係[呢度](https://codeforces.com/problemset/problem/2000/F)

呢題係分兩個 cases 睇

1. 如果得一個 rectangle 點做，同埋
2. 如果有 n 個 rectangles 又點做

第一個 case 有兩個 observations：

1. for any optimal choices，你總係可以 pick top rows and top columns。你攞第一行或者攞第二行個 cost 係一樣，個 point 亦都係一樣，咁就會有
2. 剩低嘅 一定係一個 rectangle，而且個 unchosen rectangle 嘅周界係 fixed! unchosen row + unchosen column 一定係 num row + num_column - k。

select minimum square 去 achieve $k$ 同 select maximum unchosen squares 去 achieve $k$ 係同一件事！而 maximize rectangle area subject to fixed perimeter 係愈接近 square 愈好！

所以呢個好簡單嘅 greedy algorithm 係 work！

- achieve 0 好簡單，乜都唔 pick 就得
- 要攞 $p + 1$ point，揀攞 $p$ points 之後短啲嘅 row or column，然後 update 個 rectangle

想象一吓，一開始個 rectangle 仲未係正方形嘅時候，佢會通過攞細啲嗰邊去縮長嗰一邊，直到佢變成一個正方形，然後就會梅花間竹咁 row col row col 咁儘量 keep 住佢係方形。

> 有一個 special case，就係無論如何你都攞唔到 exactly $m + n - 1$ points。當你攞剩低一個 square 嘅時候，一嘢你就會同時攞咗一個 row 同一個 col，呢個係冇所謂嘅，題目要求係 at least $k$ points。

唔難發現，個 remaining rectangle 將會係最接近 square 嘅 solution。

當我哋識得 solve 一個 rectangle，solve $n$ 個 rectangles 就可以用 dp 了

Define:
- `dp[i][p]` 係最少 color 幾個 [0..i] 個 rectangle squares 去攞 p points。
- `cost[i][q]` 係最少 color 幾個 [0..i] 個 rectangle squares 去攞 q points，呢個數用上面個 greedy 計。

咁 `dp[i][j]` 可以係 max (across all $0 \le p \le j$) `dp[i-1][p] + cost[i][j-p]`

段 code 就係咁。

```py
# https://codeforces.com/problemset/problem/2000/F

from sys import stdin

def run(k, rects):
    last_costs = None
    for (i, (m, n)) in enumerate(rects):
        only_costs = []
        row = m
        col = n
        for goal in range(k + 1):
            if goal == 0:
                cost = 0
            elif goal < m + n - 1:
                if row < col:
                    cost += row
                    col -= 1
                else:
                    cost += col
                    row -= 1
            elif goal == m + n - 1:
                # We took one more point in this case
                cost = m * n
            elif goal == m + n:
                cost = m * n
            else:
                cost = -1
            only_costs.append(cost)

        next_costs = []
        if last_costs is not None:
            for goal in range(k + 1):
                next_cost = -1
                for last in range(goal + 1):
                    only = goal - last
                    last_cost = last_costs[last]
                    only_cost = only_costs[only]
                    if last_cost != -1 and only_cost != -1:
                        next_cost_option = last_cost + only_cost
                        if next_cost == -1 or next_cost_option < next_cost:
                            next_cost = next_cost_option
                next_costs.append(next_cost)
            last_costs = next_costs
        else:
            last_costs = only_costs
    print(last_costs[-1])

def main():
    data = []
    for line in stdin:
        data.append(line.strip())
    num_test_case = int(data[0])
    line_number = 1
    for _ in range(num_test_case):
        nr, k  = [int(x) for x in data[line_number].split()]
        line_number += 1
        rectangles = []
        for _ in range(nr):
            m, n  = [int(x) for x in data[line_number].split()]
            rectangles.append((m, n))
            line_number += 1
        run(k, rectangles)

if __name__ == "__main__":
    main()
```