# CodeForces contest 2003 (Div2) 第 B 題

問題係[呢度](https://codeforces.com/problemset/problem/2003/B)

呢題個 optimal strategy 係咁，at any time，turtle delete 咗當時最細嘅 value，而 peggy 就 delete 最大嘅。

所謂 delete value，當然就係揀 contain 呢個 value 嘅一 pair，就 apply 佢個 operation 就得。

要 delete 嘅係當時嘅 maximum 或者 minimum，個 pair 保證一定係做得到。

照咁做，個最終結果就係會係 median，兩行 code 就寫完。

個 key 問題係，點解呢個 strategy 係 optimal。

我哋假設 turtle 唔聽話，delete 咗一啲唔係當時 minimum 嘅嘢。呢個有可能係大個 median，亦有可能唔係。然後 Peggy 係乖嘅，繼續 delete maximum

如果係大個 median，咁最終 turtle 得到個 value 會細過個 median，冇著數。

如果係細個 median，咁最終 turtle 依然係冇可能大個 median。

所以個 strategy 係 optimal。

Code 好明顯係 trivial，唔寫了。