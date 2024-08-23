# CodeForces contest 2001 (Div2) 第 D 題

問題係[呢度](https://codeforces.com/problemset/problem/2001/D)

呢題要求嘅怪異 permutation 其實係用 greedy 做，

假設我一開始知道對於所有可能嘅 longest subsequences 嘅第一個數字有咩 options，我自然係揀最大 so that 負咗佢嘅 lexicographically 最細

咁我揀咗 `A`，之後我有知道所有 starts with `A` 嘅 longest subsequence 後面可以有咩數字，我自然係揀最細，so that 佢嘅 lexicographically 最細

等等。

留意我揀一個 values 嘅時候，如果個 candidate set 有出現呢個 values 多於一次，我應該揀最左嘅 occurrence，咁樣可以 maximize options。

個問題關鍵後，我點知有咩可行嘅 options？

第一個 observation 係如果我係一個可行嘅 option，我同埋我後面一定要有齊所有 values。
第二個 observation 係如果我同埋我後面有齊所有 values，我一定係所有 values 嘅 last occurrence 前面。

第二個 observation 係 if and only if，如果我係所有 values 嘅 last occurrence 前面，我同埋我後面一定有齊所有 values！

所以一開始好簡單，個 candidate set 就係由第 0 個數到 min last occurrence of all values 就得。

當我揀咗一個之後，我想 reduce 個 subproblem，首先，我 delete 晒我揀個 index 同佢前面嘅嘢，咁個 array 就冇變過，跟住我 delete 埋所有我啱啱已經 select 咗嘅 value 嘅所有其它 occurrences，咁就基本上係一個一樣嘅 subproblem 了 (當然，下次係要揀 minimum)

個 high level idea 係咁，但你唔會真係做呢啲 deletion，因為好慢嘅，只要我當佢哋唔係度就得。

map 返去 low level，個 key idea 係 maintain 個 candidate set 同埋 last occurrence unselected values，佢哋需要呢啲操作。

1.）我要快速搵到 min last occurrence unselected values 去 define 個 candidate set
2.) 我要快速 update 個 candidate set
3.) 我要搵個 candidate set 嘅 min 
4.) 我要搵個 candidate set 嘅 max 

3/4 好似已經話咗我知係類此 priority queue，而 2 又可以分做

2a) 當我個 min last occurrence unselected values 因為 select 咗 values 變大嘅時候，我要 insert 一啲新 candidates，
2b) 當我 select 咗某個位置之後，我要 delete 晒前面啲可能性，同埋
2c) 當我 select 完某個 value 之後，我要 delete 晒呢個 value 嘅所有 occurrences。

只要我記住所有 value 對應嘅所有 occurrences 嘅位置，2b 同 2c 其實都只係 given 一個位置，delete 佢。

要做到晒呢啲 requirements 可以用 set。

細節嘅話 code comments 應該都有齊了

```c++
#include <iostream>
#include <set>
#include <unordered_map>
#include <vector>

using i32 = int32_t;

using namespace std;

void solve(void) {
  i32 n = 0;
  cin >> n;
  vector<i32> arr(n);
  for (int i = 0; i < n; i++) {
    cin >> arr[i];
  }

  // For each value - record all their occurrences
  unordered_map<i32, vector<i32>> occurrence;
  for (int i = 0; i < n; i++) {
    occurrence[arr[i]].push_back(i);
  }

  // Save the last index for each value
  set<i32> last;
  for (auto kvp : occurrence) {
    last.insert(kvp.second[kvp.second.size() - 1]);
  }

  // The answer must include all distinct values
  auto s = occurrence.size();

  // Value we already included in the answer
  set<i32> seen;

  // The index of the last chosen value
  i32 index = -1;

  // The algorithm below greedily select the best candidate from left to right,
  // the key question is how do we maintain the candidates. The candidates are:
  // 1.) Before the last occurrence of unselected values, and
  // 2.) After the last selected index

  // The set of all (value, index) that are candidates for the next value,
  // sorted by value and then by index, so that the min is what we want when we
  // want the min value (and smaller index on ties)
  set<pair<i32, i32>> mins;

  // The set of all (-value, index) that are candidates for the next value,
  // sorted by value and then by index, so that the min (with negated value) is
  // what we want when we want the max value (ans smaller index on ties)
  set<pair<i32, i32>> maxs;

  // When a certain value is selected, last occurrence of unselected values may
  // moves to the right. There is no need to reinsert inserted value, therefore
  // this value denote the inclusive start where we should start marching the
  // insert value up to the last occurrence of unselected values
  i32 candidate_insertion_start = 0;

  // The buffer for storing the answer
  vector<i32> answer(s);

  for (i32 t = 0; t < s; t++) {
    // Here is the last occurrence of unselected values
    int last_unselected_occurrence = *last.begin();

    // Increase by one so that the loop below includes the unselected value
    last_unselected_occurrence++;

    for (i32 i = candidate_insertion_start; i < last_unselected_occurrence;
         i++) {
      // If we have already picked some value v before, but we see v again when
      // we prepare candidates, skip it
      if (seen.find(arr[i]) == seen.end()) {
        mins.insert(make_pair(arr[i], i));
        maxs.insert(make_pair(-arr[i], i));
      }
    }

    // When the next insertion happens, this is where we should start
    // This also avoids any walk if the last occurrence of unselected values
    // does not change
    candidate_insertion_start = last_unselected_occurrence;

    // Pick the value from the candidates
    auto value = (t % 2 == 0) ? *maxs.begin() : *mins.begin();
    i32 chosen_value = abs(value.first);
    i32 chosen_index = value.second;
    answer[t] = chosen_value;

    // Delete everything before it. Note that it is possible that something
    // before it is already deleted (e.g. it is an extra occurence of some
    // earlier chosen value
    //
    // Note that index is the last chosen occurrence, so it (and everything
    // before it) is already removed
    //
    for (i32 i = index + 1; i <= chosen_index; i++) {
      {
        auto probe = mins.find(make_pair(arr[i], i));
        if (probe != mins.end()) {
          mins.erase(probe);
        }
      }
      {
        auto probe = maxs.find(make_pair(-arr[i], i));
        if (probe != maxs.end()) {
          maxs.erase(probe);
        }
      }
    }
    // Delete all occurrence of the chosen value. Note that it is possible that
    // something before it is already deleted (e.g. it is an extra earlier
    // occurence).
    for (auto i : occurrence[answer[t]]) {
      {
        auto probe = mins.find(make_pair(arr[i], i));
        if (probe != mins.end()) {
          mins.erase(probe);
        }
      }
      {
        auto probe = maxs.find(make_pair(-arr[i], i));
        if (probe != maxs.end()) {
          maxs.erase(probe);
        }
      }
    }

    // Remove the last occurrence of the chosen value from last, so last remains
    // the last unchosen occurrences
    last.erase(occurrence[chosen_value][occurrence[chosen_value].size() - 1]);

    // Mark the chosen value as seen
    seen.insert(chosen_value);

    // This allow us to remove less value in the next iteration
    index = chosen_index;
  }

  cout << s << endl;
  for (i32 i = 0; i < s; i++) {
    cout << answer[i] << " ";
  }
  cout << endl;
}

int main(void) {
  ios::sync_with_stdio(false);
  cin.tie(0);
  i32 test_cases = 0;
  cin >> test_cases;
  while (test_cases--) {
    solve();
  }
  return 0;
}
```