# 用 Segment Tree 解「covered weighted sum」問題調查總結（廣東話）

## 問題描述

有一條長度為 `n` 嘅 array，每個 position `i` 有一個固定嘅非負 weight `w[i]`。過程中會動態做以下 operations：
- 插入一個 interval `[l, r)`：對所有 `l ≤ i < r`，令 `count[i] += 1`。
- 刪除一個 interval `[l, r)`：對所有 `l ≤ i < r`，令 `count[i] -= 1`。

假設永遠保持 `count[i] ≥ 0`。

Query 係：
對於一個 interval `[L, R)`，計

 $\sum_{i \in [L,R), \, count[i] \neq 0} w[i]$ 

即係：喺 `[L, R)` 入面，所有有被 cover 到（`count[i] > 0`）嘅 positions，佢哋嘅 weights 總和，叫做「covered weighted sum」。

目標：
- 動態支援多次「插入 interval / 刪除 interval / 查詢 covered weighted sum」，每個 operation 盡量做到 $O(\log n)$ 。

---

## 難點分析

1. **暴力維護 `count[i]` 太慢**
每次對 interval `[l, r)` 暴力逐個 position 更新 `count[i]`，更新一次要 $O(n)$ ，多次操作會唔實際。

2. **Query 唔係單純 min / sum**
- `min(count[i])` 或 `sum(w[i])` 呢啲 standard summary 唔夠用。
- 需要分清楚 `count[i] = 0` 同 `count[i] > 0`，只計後者嘅 weights。

3. **要同時支援 range add 同呢個特殊 query**
Lazy propagation 常見用法係「range add + range sum / min」，但依家 query 係「sum over `count != 0`」，要設計一個可以合併、又可以 lazy update 嘅 node 摘要。

4. **要有可以 merge 嘅抽象狀態**
每個 node summary 要：
- 可以由左右 child deterministic merge；
- query 對一堆 disjoint nodes，用同一套 merge 規則合成一個 summary，再由 summary 得出答案。

---

## 解法：Node 摘要設計（演算法層面）

### Node 儲存嘅資訊

對 Segment Tree 上一個代表 interval `S` 嘅 node，儲存：

- `min_val`：
- $\text{min-val} = \min_{i \in S} \, count[i]$ 
- 因為保證 `count[i] ≥ 0`，所以 $\text{min-val} \ge 0$ 。

- `sum_w`：
- $\text{sum-w} = \sum_{i \in S} w[i]$ 
- 同 `count` 無關，只係 interval 嘅 weights 總和。

- `sum_w_not_min`：
- 定義：
 $\text{sum-w-not-min} = \sum_{\substack{i \in S \\ count[i] \neq \text{min-val}}} w[i]$ 
- 即係：喺呢個 interval 入面，所有 `count[i]` 唔等於最小值嗰啲 positions 嘅 weights 總和。

- `lazy`：
- 對整個 interval 懸而未決嘅 `+v`，即所有 `count[i]` 喺呢個 node 下其實都要加 `v`。

### Merge 兩個 child nodes

左 child `L`、右 child `R` 嘅 summary：

- $L.\text{min-val}, \; L.\text{sum-w}, \; L.\text{sum-w-not-min}$ 
- $R.\text{min-val}, \; R.\text{sum-w}, \; R.\text{sum-w-not-min}$ 

父 node `P`：

- $P.\text{sum-w} = L.\text{sum-w} + R.\text{sum-w}$ 

`min_val` 同 `sum_w_not_min` 規則：

1. 如果 $L.\text{min-val} < R.\text{min-val}$ ：
- $P.\text{min-val} = L.\text{min-val}$ 
- 最小值淨係喺左邊出現，右邊全部 positions 都係「count ≠ min」， $P.\text{sum-w-not-min} = L.\text{sum-w-not-min} + R.\text{sum-w}$ 

2. 如果 $R.\text{min-val} < L.\text{min-val}$ （對稱）：
- $P.\text{min-val} = R.\text{min-val}$ 
- 最小值淨係喺右邊，左邊全部 positions 都係「count ≠ min」， $P.\text{sum-w-not-min} = R.\text{sum-w-not-min} + L.\text{sum-w}$ 

3. 如果 $L.\text{min-val} = R.\text{min-val}$ ：
- $P.\text{min-val} = L.\text{min-val}$ 
- 左右都有 positions 達到最小值， $P.\text{sum-w-not-min} = L.\text{sum-w-not-min} + R.\text{sum-w-not-min}$ 

呢套 merge 規則保證 parent summary 可以完全由 children summaries 計到。

### Lazy add 點處理？

對某個 node interval `S` 做 uniform `+v`（所有 `count[i] += v`）：

- $\text{min-val} \leftarrow \text{min-val} + v$ 
- `sum_w` 唔變（weights 無變）。
- `sum_w_not_min` 唔變：
- 因為全部 positions 同時加 `v`，最小值本身升咗，但「邊啲 positions 係 global minimum」嗰個 subset 冇變，
所以「count ≠ min」嗰個 subset 亦冇變。
- `lazy += v`（之後再 `_push` 落 child）。

重點係：lazy update 只係 shift 最小值，唔洗重算 `sum_w_not_min`。

### Query：由 summary 得到 covered weighted sum

對一個完整覆蓋 query interval 嘅 summary
 $(\text{min-val}, \text{sum-w}, \text{sum-w-not-min})$ ：

- 因為 $count[i] \ge 0$ ，所以 $\text{min-val} \ge 0$ 。

分兩個情況：

1. $\text{min-val} > 0$ ：
- interval 入面每個 position $count[i] \ge 1$ ，即全部都 cover。
- Query 要求 sum over `count[i] ≠ 0`，即所有 positions： $\text{Ans} = \text{sum-w}$ 

2. $\text{min-val} = 0$ ：
- 有 positions $count[i] = 0$ ，有 positions $> 0$ 。
- `sum_w_not_min` 定義係 sum over $count[i] \neq \text{min-val}$ ，而此時 $\text{min-val} = 0$ ，
所以就係 sum over $count[i] \neq 0$ 嘅 weights： $\text{Ans} = \text{sum-w-not-min}$ 

所以，只要 query 覆蓋嘅 nodes 可以 merge 成一個總 summary，就可以直接用 `(min_val, sum_w, sum_w_not_min)` 計到 covered weighted sum。

---

## Query 抽象：多個 nodes merge 成一個 summary

Segment Tree 查詢 interval `[L, R)` 會拆成 $O(\log n)$ 個互不重疊 nodes：
 $S_1, S_2, \dots, S_k$ 。

做法：

1. 用一個 accumulator 儲存整個 query interval 嘅 summary：
- 初始：「空」狀態：
- $\text{acc-min} = +\infty$ 
- $\text{acc-sum-w} = 0$ 
- $\text{acc-sum-w-not-min} = 0$ 

2. 每遇到一個完全覆蓋嘅 node，就用同一套 merge 規則將 node summary merge 入 accumulator。

3. 最後 accumulator 就係整個 `[L, R)` 嘅 summary
 $(\text{acc-min}, \text{acc-sum-w}, \text{acc-sum-w-not-min})$ ：
- 如果 $\text{acc-min} > 0$ ：答案 $= \text{acc-sum-w}$ 。
- 如果 $\text{acc-min} = 0$ ：答案 $= \text{acc-sum-w-not-min}$ 。

---

## 測試策略：Brute force 同 insert/delete pair harness

### Brute force 抽象

Brute force 嘅 data structure：

- 顯式儲存一個 array `count[i]`。
- `range_add(l, r, v)`：loop `i = l..r-1` 去做 `count[i] += v`，並 assert `count[i] ≥ 0`。
- `query(l, r)`：loop `i = l..r-1`，如果 `count[i] != 0`，就把 `w[i]` 加入答案。

雖然時間複雜度係 $O(n)$ ，但 logic 清晰，非常適合作 reference 去 verify Segment Tree correctness。

### Harness：用 insert/delete pair 保證 `count >= 0`

為咗同 Segment Tree 做隨機測試，而且又要保證 $count[i] \ge 0$ ，測試 harness 可以咁設計：

1. 預先 generate $\text{num-intervals}$ 個 random intervals `[l_j, r_j)`。

2. 每個 interval `j` 定義兩個 operations：
- `INSERT j`：對 `[l_j, r_j)` 做 $count[i] \mathrel{+}= 1$ 。
- `DELETE j`：對 `[l_j, r_j)` 做 $count[i] \mathrel{-}= 1$ 。

3. 維護一個 operation pool：
- 初始 pool：只包含所有 `INSERT j`。
- 當揀中 `INSERT j`：
- 對 Segment Tree 同 BruteDS 呼叫 `range_add(l_j, r_j, +1)`。
- 標記 interval `j` 為 active。
- 把 `DELETE j` 加入 operation pool。

- 當揀中 `DELETE j`：
- 因為之前必定做過相同次數嘅 `INSERT j`，所以對應 positions 嘅 `count[i]` 一定大於 0。
- 對兩個 data structure 呼叫 `range_add(l_j, r_j, -1)`。
- 標記 interval `j` 為 inactive。
- 把 `INSERT j` 再次加回 pool。

4. 操作流程：
- 重複好多次：
- 以某個機率做 query：random 揀一個 `[L, R)`，比較 Segment Tree 同 BruteDS 嘅答案。
- 否則喺 pool 度 random 揀一個 operation (`INSERT` / `DELETE`)，照上述邏輯處理。

呢個設計自然保證：

- 每個 `DELETE j` 一定喺某次 `INSERT j` 之後先出現，
- 每個 interval 上嘅 net 加數永遠唔會變負數，
- 所有 positions 嘅 $count[i] \ge 0$ ，滿足 node summary 設計入面對 `min_val` 嘅假設。

---

## 抽象總結：Segment Tree 同 Lazy Propagation 要求嘅性質

### Segment Tree 層面

想用 Segment Tree 解問題，一般要滿足：

1. **Node summary(S) 定義良好**
每個 node 對應一個 interval `S`，有一個 summary(S) 抽象，包含所有 query 相關資訊。

2. **可以 deterministic merge**
存在一個 merge 函數： $\text{summary}(P) = \text{merge}(\text{summary}(L), \text{summary}(R))$ 
父 node summary 可以純粹經由左右 child summary 計出，不需要額外資訊。

3. **Query 可以拆成多個 node summary 合併**
Query interval `[L, R)` 可以表達成若干個 disjoint nodes 嘅 union，
把 summary 用同一個 merge 規則疊加，最後得到一個 overall summary，
再由 overall summary 拿到答案。

今次嘅設計入面：
- $\text{summary}(S) = (\text{min-val}, \text{sum-w}, \text{sum-w-not-min})$ ；
- merge 規則明確定義；
- query 時用 accumulator 按 canonical cover nodes 合併。

### Lazy Propagation 層面

Lazy propagation 係為咗處理「range update」而唔洗每次推到 leaf，抽象上需要：

1. **Update 對 summary 嘅影響可以 local 計到**
要有 $\text{apply-update}(\text{summary}(S), u)$ ，唔睇 children 都可以算出更新後嘅 summary。
呢度嘅 `u` 係「整段 $count[i] \mathrel{+}= v$ 」，而我哋做到：
- $\text{min-val} \leftarrow \text{min-val} + v$ ；
- `sum_w` 不變；
- `sum_w_not_min` 不變。

2. **Update 同 merge 相容（可交換）**
- 先 merge 再 apply update，或者先對 children apply update 再 merge，結果要一致。
- 由於 update 係 uniform（所有 positions 加同一個 value），呢個條件係滿足嘅。

3. **可以 $O(1)$ 把 lazy 推落 children**
- `_push` 時，只靠 children 原本嘅 summary 同 lazy value，就可以算出 children 新嘅 summary，唔洗拆到 leaf。

喺呢個 covered weighted sum 問題中，上述性質全部滿足，所以可以用 Segment Tree + Lazy Propagation 高效支援 dynamic insert / delete / query。

```py
import random
import math

class SegNode:
    __slots__ = ("min_val", "sum_w", "sum_w_not_min", "lazy")
    def __init__(self, min_val=0, sum_w=0, sum_w_not_min=0, lazy=0):
        self.min_val = min_val
        self.sum_w = sum_w
        self.sum_w_not_min = sum_w_not_min
        self.lazy = lazy

class SegTree:
    """
    Segment tree for:
      - range_add(l, r, v): count[i] += v for i in [l, r)
      - query(l, r): sum(weights[i] for i in [l,r) if count[i] != 0)

    Node summary:
      min_val: minimum count in segment
      sum_w: sum of weights in segment (fixed)
      sum_w_not_min: sum of weights at positions where count != min_val
      lazy: pending uniform add to counts in this segment

    Query interpretation on a segment S:
      If global min_val > 0: all counts > 0 => answer = sum_w
      If global min_val == 0: zeros are exactly at positions with count == min_val;
        answer = sum_w_not_min (weights where count != min_val).
    """

    def __init__(self, weights):
        self.n = len(weights)
        self.weights = weights
        size = 4 * self.n if self.n > 0 else 1
        self.tree = [SegNode() for _ in range(size)]
        if self.n > 0:
            self._build(1, 0, self.n - 1)

    # ---------- Building and merging ----------

    def _build(self, idx, l, r):
        if l == r:
            w = self.weights[l]
            # Initially count[i] = 0
            self.tree[idx].min_val = 0
            self.tree[idx].sum_w = w
            # All positions have count == min => sum_w_not_min = 0
            self.tree[idx].sum_w_not_min = 0
            self.tree[idx].lazy = 0
            return
        m = (l + r) // 2
        self._build(idx * 2, l, m)
        self._build(idx * 2 + 1, m + 1, r)
        self._pull(idx)

    def _merge_nodes(self, left: SegNode, right: SegNode) -> SegNode:
        """
        Combine summaries of left and right into a new SegNode.
        """
        res = SegNode()
        res.lazy = 0  # merged node has no lazy; parent controls lazy

        # Weighted sum always adds
        res.sum_w = left.sum_w + right.sum_w

        # Merge min_val and sum_w_not_min based on min_val comparison
        if left.min_val < right.min_val:
            res.min_val = left.min_val
            # positions with min are in left only; right contribution is all weights
            res.sum_w_not_min = left.sum_w_not_min + right.sum_w
        elif right.min_val < left.min_val:
            res.min_val = right.min_val
            res.sum_w_not_min = right.sum_w_not_min + left.sum_w
        else:
            # same min
            res.min_val = left.min_val
            res.sum_w_not_min = left.sum_w_not_min + right.sum_w_not_min

        return res

    def _pull(self, idx):
        """
        Recompute node idx from its two children.
        Assumes children have up-to-date summaries and lazy already applied to them.
        """
        left = self.tree[idx * 2]
        right = self.tree[idx * 2 + 1]
        self.tree[idx] = self._merge_nodes(left, right)

    # ---------- Lazy propagation ----------

    def _apply_lazy(self, idx, add):
        """
        Apply uniform add 'add' to this node's counts.

        Because we only track min_val and which positions are at min,
        and sum_w_not_min = sum(weights[i] where count != min_val),
        a uniform shift does NOT change who is at the minimum.
        So:
          min_val   += add
          sum_w     unchanged
          sum_w_not_min unchanged
          lazy      += add
        """
        node = self.tree[idx]
        node.min_val += add
        node.lazy += add
        # sum_w and sum_w_not_min unchanged

    def _push(self, idx):
        """
        Push lazy value at idx down to its children.
        """
        node = self.tree[idx]
        if node.lazy != 0:
            add = node.lazy
            # apply to children
            self._apply_lazy(idx * 2, add)
            self._apply_lazy(idx * 2 + 1, add)
            node.lazy = 0

    # ---------- Public operations ----------

    def range_add(self, ql, qr, v):
        """
        Add v to count[i] for i in [ql, qr).
        """
        if self.n == 0:
            return
        ql = max(0, ql)
        qr = min(self.n, qr)
        if ql >= qr:
            return
        self._range_add(1, 0, self.n - 1, ql, qr - 1, v)

    def _range_add(self, idx, l, r, ql, qr, v):
        # no overlap
        if r < ql or l > qr:
            return
        # fully inside
        if ql <= l and r <= qr:
            self._apply_lazy(idx, v)
            return
        # partial
        self._push(idx)
        m = (l + r) // 2
        self._range_add(idx * 2, l, m, ql, qr, v)
        self._range_add(idx * 2 + 1, m + 1, r, ql, qr, v)
        self._pull(idx)

    def query(self, ql, qr):
        """
        Return sum(weights[i] for i in [ql, qr) if count[i] != 0).
        """
        if self.n == 0:
            return 0
        ql = max(0, ql)
        qr = min(self.n, qr)
        if ql >= qr:
            return 0

        # We will accumulate a combined summary over the query range.
        # Start with a "neutral" node: min_val = +inf, sum_w = 0, sum_w_not_min = 0.
        self._acc_min = math.inf
        self._acc_sum_w = 0
        self._acc_sum_w_not_min = 0

        self._query_collect(1, 0, self.n - 1, ql, qr - 1)

        # Now interpret the combined summary:
        if self._acc_min > 0:
            return self._acc_sum_w
        else:
            return self._acc_sum_w_not_min

    def _acc_merge(self, node: SegNode):
        """
        Merge a node's summary into the accumulator for the query.
        This is like merging two children, where the accumulator acts as the left,
        and 'node' acts as the right.
        """
        if self._acc_min is math.inf:
            # accumulator is empty, just take node's summary
            self._acc_min = node.min_val
            self._acc_sum_w = node.sum_w
            self._acc_sum_w_not_min = node.sum_w_not_min
            return

        # Combine accumulator (A) and node (B) using the same logic as merge
        A_min = self._acc_min
        A_w = self._acc_sum_w
        A_not_min = self._acc_sum_w_not_min

        B_min = node.min_val
        B_w = node.sum_w
        B_not_min = node.sum_w_not_min

        # New weighted sum: sum of both
        new_w = A_w + B_w

        # New min and sum_w_not_min
        if A_min < B_min:
            new_min = A_min
            new_not_min = A_not_min + B_w
        elif B_min < A_min:
            new_min = B_min
            new_not_min = B_not_min + A_w
        else:
            new_min = A_min
            new_not_min = A_not_min + B_not_min

        self._acc_min = new_min
        self._acc_sum_w = new_w
        self._acc_sum_w_not_min = new_not_min

    def _query_collect(self, idx, l, r, ql, qr):
        # no overlap
        if r < ql or l > qr:
            return
        # fully inside
        if ql <= l and r <= qr:
            self._acc_merge(self.tree[idx])
            return
        # partial
        self._push(idx)
        m = (l + r) // 2
        self._query_collect(idx * 2, l, m, ql, qr)
        self._query_collect(idx * 2 + 1, m + 1, r, ql, qr)

class BruteDS:
    """
    Brute-force reference:
      - count[i] explicitly stored
      - update: count[i] += v
      - query: sum(weights[i] for i in [l,r) if count[i] != 0)
    """

    def __init__(self, weights):
        self.weights = weights
        self.n = len(weights)
        self.count = [0] * self.n

    def range_add(self, l, r, v):
        l = max(0, l)
        r = min(self.n, r)
        for i in range(l, r):
            self.count[i] += v
            # For sanity in tests: enforce nonnegative if that's desired
            # assert self.count[i] >= 0

    def query(self, l, r):
        l = max(0, l)
        r = min(self.n, r)
        s = 0
        for i in range(l, r):
            if self.count[i] != 0:
                s += self.weights[i]
        return s

def random_test_pairs(
    num_tests=50,
    n=20,
    max_weight=10,
    num_intervals=30,
    num_ops=2000,
):
    """
    Randomly test SegTree against BruteDS using an insert/delete-pair scheme.

    Scheme:
      - Pre-generate `num_intervals` random intervals [l, r).
      - For each interval j, define two operations:
          * INSERT j: add +1 on that interval
          * DELETE j: add -1 on that interval
      - Maintain a pool of operations:
          * Initially, all INSERT j are present, DELETE j are absent.
          * When we execute INSERT j, we add DELETE j to the pool (interval becomes active).
          * When we execute DELETE j, we add INSERT j to the pool (interval becomes inactive).
      - At each step, pick a random operation from the pool.
      - Interleave random queries.
      - This guarantees that DELETE j never happens before INSERT j, so count[i] ≥ 0 always.
    """
    rng = random.Random(0)

    for t in range(num_tests):
        # Random weights
        weights = [rng.randint(1, max_weight) for _ in range(n)]
        seg = SegTree(weights)
        brute = BruteDS(weights)

        # Pre-generate intervals [l_j, r_j)
        intervals = []
        for _ in range(num_intervals):
            l = rng.randint(0, n - 1)
            r = rng.randint(l + 1, n)
            intervals.append((l, r))

        # For each interval j, we maintain whether it's currently active.
        active = [False] * num_intervals

        # Operation pool: list of ("ins"/"del", j)
        # Start with all inserts available.
        op_pool = [("ins", j) for j in range(num_intervals)]

        for _ in range(num_ops):
            # With some probability, do a query instead of an update.
            if rng.random() < 0.4:
                # Query
                l = rng.randint(0, n - 1)
                r = rng.randint(l + 1, n)
                ans_seg = seg.query(l, r)
                ans_brute = brute.query(l, r)
                if ans_seg != ans_brute:
                    print("Mismatch in query!")
                    print(f"weights = {weights}")
                    print(f"counts  = {brute.count}")
                    print(f"intervals = {intervals}")
                    print(f"query [{l},{r}): seg={ans_seg}, brute={ans_brute}")
                    return
            else:
                # Update: pick a random op from the pool
                if not op_pool:
                    # If somehow empty, just do a query
                    l = rng.randint(0, n - 1)
                    r = rng.randint(l + 1, n)
                    ans_seg = seg.query(l, r)
                    ans_brute = brute.query(l, r)
                    if ans_seg != ans_brute:
                        print("Mismatch in query (empty pool)!")
                        print(f"weights = {weights}")
                        print(f"counts  = {brute.count}")
                        print(f"intervals = {intervals}")
                        print(f"query [{l},{r}): seg={ans_seg}, brute={ans_brute}")
                        return
                    continue

                k = rng.randrange(len(op_pool))
                op, j = op_pool[k]
                # Remove chosen op from pool
                op_pool[k] = op_pool[-1]
                op_pool.pop()

                l, r = intervals[j]

                if op == "ins":
                    # Insert interval j: add +1 on [l, r)
                    seg.range_add(l, r, +1)
                    brute.range_add(l, r, +1)
                    # Mark active and add its delete op to pool
                    assert not active[j]
                    active[j] = True
                    op_pool.append(("del", j))
                else:
                    # Delete interval j: add -1 on [l, r)
                    seg.range_add(l, r, -1)
                    brute.range_add(l, r, -1)
                    # Mark inactive and add its insert op back to pool
                    assert active[j]
                    active[j] = False
                    op_pool.append(("ins", j))

                # Sanity check: counts must be >= 0
                for c in brute.count:
                    if c < 0:
                        print("Negative count encountered, which should be impossible.")
                        print(f"weights = {weights}")
                        print(f"counts  = {brute.count}")
                        print(f"intervals = {intervals}")
                        return

    print("All randomized tests with insert/delete pairs passed!")

if __name__ == "__main__":
    random_test_pairs()
```