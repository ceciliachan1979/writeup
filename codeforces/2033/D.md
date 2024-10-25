## CodeForces contest 2033 (Div3) 第 D 題

問題係[呢度](https://codeforces.com/contest/2033/problem/D)

呢題嘅實作靈感來源係嚟自 LeetCode 嘅 [Two Sums](https://leetcode.com/problems/two-sum/description/)，
雖然係 Easy, 但係玩法有好多：Bruce Force， Linear Search，Binary Search 同用 Hash Table。

打嗰陣諗起呢個解法：https://leetcode.com/submissions/detail/1430992439 ,所以就試下招，結果順利拎到 AC

Key Idea: 如果 perfix sum 係個 Hash Map 入邊，而且個 index 冇過界，咁就代表揾到一個啱嘅 segment。

我地睇一個例子就會明：
```
      i
      |
      V
a = [12, 4, -4, 1], 呢個時候，last_end 係 -1, perfix_sum = 12, cache[perfix_sum] = 0  <---------
                                                                                             |
         i                                                                                   |
         |                                                                                   |
         V                                                                                   |
a = [12, 4, -4, 1], 呢個時候，last_end 係 -1, perfix_sum = 12 + 4, cache[perfix_sum] = 1       |
                                                                                             |
             i                                                                               |
             |                                                                               |
             V                                                                               |
a = [12, 4, -4, 1], 呢個時候，last_end 係 -1, perfix_sum = 12 + 4 - 4,                         |
　　　　　　　　　　　　呢個 moment 就好重要，你會見到 perfix_sum 變返做 12，                         |
                    咁即係 cache[perfix_sum] = 0 >--------------------------------------------^ 
                    即係代表，中間嘅加埋係 `0`, 所以我地就揾到一個啱嘅 segment
```

最後, 我地 reset hash map 嘅時候要用 `i`，
因為我地嘅 segment 右邊嘅 index 唔可以細過左邊嘅 index，亦都唔可以 overlap，
所以一定要 set 做 `i`

```cpp
#pragma GCC optimize("Ofast,unroll-loops")
  
#include <bits/stdc++.h>
  
using namespace std;
  
using i64 = int64_t;
  
void solve(void) {
  i64 n;
  cin >> n;
  
  vector<i64> a(n, 0);
  for (auto& e : a) {
    cin >> e;
  }
  
  i64 prefix_sum = 0;
  i64 count      = 0;
  
  map<i64, i64> cache;
  cache[0] = -1;
  
  i64 last_end = -1;
  i64 m        = a.size();
  
  for (i64 i = 0; i < m; ++i) {
    prefix_sum += a[i];
  
    if (cache.find(prefix_sum) != cache.end()) {
      if (auto j = cache[prefix_sum]; j >= last_end) {
        count++;
        last_end   = i;
        prefix_sum = 0;
  
        cache.clear();
        cache[0] = i;
      }
    }
    cache[prefix_sum] = i;
  }
  cout << count << "\n";
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