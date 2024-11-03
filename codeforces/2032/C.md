## CodeForces contest 2032 (Div3) 第 C 題

問題係[呢度](https://codeforces.com/contest/2032/problem/C)

呢題其實我已經諗到大部份 editorial 講嘅嘢，就係諗唔到最後一步，唉 ⋯！

首先，如果我哋將個 array sort 咗佢，可以好快速咁檢查係咪全部 triple 都係 三角形

其實呢點好簡單，因為如果最細嗰兩個同最大嗰個都 work，就全部都會 work。

咁唔 work 點算呢，我哋仲有第二招，可以 delete 嘢！雖然個 problem 淨係俾我哋 copy array element，但其實因為最終 array 總會係 non-empty，而 copy 個 element 整到佢同最終 array 裡面嘅其中一個 element 一樣，其實同 delete 咗佢冇分別，所以我哋可以 delete element。

咁 delete 邊個呢？

我用咗呢個（錯誤）嘅諗法睇：

`1 2 3 4 5 6 7` 1 + 2 = 3 <= 7 係當然唔得。

我要求條尾減頭兩個係 < 0，依家係 7 - 1 - 2 = 4 唔係 < 0

delete `1` 嘅話，7 - 2 - 3 = 2 都唔係 < 0
delete `7` 嘅話，6 - 1 - 2 = 3 都唔係 < 0

但係 delete `1` 好似快啲達到目標，咁就揀 delete `1`，greedy 嘅諗法。

如果我每次都揀更著數嘅 operations，咁應該係 minimal number of operations﹐事實上，我呢個諗法都過到好多 test cases。

除咗一個 special case，如果 delete 頭同 delete 尾一樣著數呢？

Try both？一直都一樣咁點算？

就係呢個位 kick 住了，所以比賽放棄咗呢題。

睇返 editorial，佢唔係用 greedy，佢只係試晒所有 cases。

我哋可以 fix 咗每一個可能 deleted prefix，死人都唔再 delete 個 prefix，計 suffix 要 delete 幾多個。

後面其實唔難搞，我用咗 binary search，其實用一枝 pointer 向後行一樣得。

得咗。

今次都唔知點樣總結好，或者咁講啦，greedy 唔 work，有冇快速嘅方法 exhaust 晒 d 可能性？

```py
from __future__ import annotations
from typing import List
 
from sys import stdin
from bisect import bisect_left
 
def run(arr):
    arr.sort()
    n = len(arr)
    best = n - 2
    for i in range(n - 2):
        bound = arr[i] + arr[i + 1]
        exclusive = bisect_left(arr, bound, i + 1, n)
        length = exclusive - i
        delete = n - length
        best = min(best, delete)
    print(best)
 
def main():
    data = []
    for line in stdin:
        data.append(line.strip())
    num_test_case = int(data[0])
    line_number = 1
    for _ in range(num_test_case):
        n = int(data[line_number])
        line_number += 1
        arr = [int(x) for x in data[line_number].split()]
        line_number += 1
        run(arr)
 
if __name__ == "__main__":
    main()
```