## [Codeforces Round 983 (Div. 2)](https://codeforces.com/contest/2032) 第 A 題

問題係[呢度](https://codeforces.com/contest/2032/problem/A)

Lemma 0：因為 switch 嘅數量保證係燈嘅 2 倍，而且每一個 switch 只可以駁一盞燈，所以任意兩個嘅 switch 嘅 status 就可以決定燈嘅 status

Lemma 1：兩個嘅 switch 嘅 status 唔一樣，咁燈嘅 status 先可以係 1，否則一定係 0

minimum 嘅情況：

由 Lemma 1 可以得知，只要兩個嘅 switch 嘅 status 一樣，咁燈嘅 status 一定係 0。所以我地可以每一次配對，可以㨂兩個 1 或 兩個 0 作為一對，
因為 switch 嘅數量係 even，所以結果只有兩個：一係全部嘅 pair 都係 (0，0) 或 (1, 1)，一係只有一對係 (0, 1)，其他全部係 (0, 0) 或 (1, 1)
由於 even ＝ even + even，或者 even ＝ odd + odd，所以我地只要 check 下 1 嘅總數係唔係 odd 就 okay.

maximum 嘅情況：

由 Lemma 1 可以得知，只要兩個嘅 switch 嘅 status 唔一樣，咁燈嘅 status 一定係 1。所以我地可以每一次配對，可以㨂一個 1 同一個 0 作為一對，
我地可以知道總共有幾多個 1，同總共有幾多個 0，咁 take min（總共有幾多個 1，總共有幾多個 0）就可以得到 maximum 嘅 (0, 1) pairs

```cpp
#pragma GCC optimize("Ofast,unroll-loops")
  
#include <bits/stdc++.h>
  
using namespace std;
  
using i64 = int64_t;
  
void solve(void) {
  i64 n;
  cin >> n;
  
  i64 switchs = 2 * n;
  
  i64 ones = 0;
  for (i64 i = 0; i < switchs; ++i) {
    if (i64 a = 0; cin >> a && a == 1) {
      ones++;
    }
  }
  
  cout << ones % 2 << " " << min(ones, switchs - ones) << "\n";
}
  
int main(void) {
  ios::sync_with_stdio(false);
  cin.tie(0);
  i64 test_cases = 0;
  cin >> test_cases;
  while (test_cases--) {
    solve();
  }
  return 0;
}
```

作者：Cecilia
整理：Gapry