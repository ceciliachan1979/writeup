## [CodeChef Starters 158](https://www.codechef.com/START158), Largest Subsequence

問題係[呢度](https://www.codechef.com/problems/LARGESUB)

## 分析

Claim: 
- `n`    就係表示 `S` 嘅長度
- `F(S)` 就係表示 `S` 嘅第一個 char
- `B(S)` 就係表示 `S` 嘅最尾嘅 char
- `L(S)` 就係表示 `the length of the largest subsequence of S`
- `P(S)` 就係表示 `The number of occurrences of "ab" as a substring of S` 
- `Q(S)` 就係表示 `The number of occurrences of "ba" as a substring of S` 

Thm 1: 只要 `F(S)` 等於 `B(S)`, 咁 `L(S)` 就係 `n`   

Proof By Case:
- Part 1, F(S) = B(S) = a
  - n = 3 
    - S = aaa, P(S) = Q(S) = 0, L(S) = n 
    - S = aba, P(S) = Q(S) = 1, L(S) = n
  - n = 4 
    - S = aaaa, P(S) = Q(S) = 0, L(S) = n 
    - S = abaa, P(S) = Q(S) = 1, L(S) = n
    - S = aaba, P(S) = Q(S) = 1, L(S) = n
    - S = abba, P(S) = Q(S) = 1, L(S) = n
  - n = 5
    - S = aaaaa              , P(S) = Q(S) = 0, L(S) = n 
    - S = abaaa, aabaa, aaaba, P(S) = Q(S) = 1, L(S) = n 
    - S = abbaa, aabba       , P(S) = Q(S) = 1, L(S) = n 
    - S = ababa              , P(S) = Q(S) = 2, L(S) = n
    - S = abbba              , P(S) = Q(S) = 1, L(S) = n 
- Part 2, F(S) = B(S) = b 
  個道理同 Part 1 一樣，只係由 a 變咗做 b

跟住落嚟我地就要解決，當 `F(S)` 唔等於 `B(S)` 嘅時候，我地要點做？
其實都好簡單，我地只要揾到一個 substring `T` of `S`，而 `F(T)` 係會等於 `B(T)` 就得

Thm 2: 當 `F(S)` 唔等於 `B(S)` 嘅時候, 會存在一個 substring `T` of `S` 令到 `L(Ｓ)` 等於 `Ｔ` 嘅長度   

claim: 
- `start` 係表示由 `S` 嘅最頭開始，第一個重複 `S` 嘅第一個 char 嘅 substring 嘅長度
- `end` 係表示由 `S` 嘅最尾開始，第一個重複 `S` 嘅最尾一個 char 嘅 substring 嘅長度

Proof By Case:

Case 1:
```
           end = 2
            |
            V
S = aaaabaaabb
       ^
       |
     start = 4

L(S) = T = n - min(start, end) = n - end = n - 2
```

Case 2:
```
         end = 4
          |
          V
S = aabaaabbbb
     ^
     |
 start = 2

L(S) = T = n - min(start, end) = n - start = n - 2
```

## 答案
```cpp
i64 n;
string s;

cin >> n;
cin >> s;

const auto& f = s.front();
const auto& b = s.back();

if(f == b) {
  cout << n << "\n";
  return;
} 

i64 start = 0;
while(s[start] == f) {
  start++;
}

i64 end = 0;
while(s[n - 1 - end] == b) {
  end++;
}
cout << n - min(start, end) << "\n";
```

作者：Cecilia, 整理：Gapry