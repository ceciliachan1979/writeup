寫一下 anyhow-python 嘅創作理念

又到節日，就想玩吓嘢，創作。適逢見到個畫星星 post，就想試吓寫 obfuscated program 去做呢個 task。

Obfuscation 係指故意混淆代碼使其難以理解，呢個做法同 software engineering 嘅理念基本上係背道而馳，然而，在合適嘅場合，呢啲技巧係有用。例如防止抄襲，反編譯，反破解等等。

咁咩代碼難理解呢？長鏈嘅因果關係就很難理解， A 導致 B 導致 C 導致 D 導致 E 等等 ⋯ 只給你 A，你通常只能一步一步做才知道最終結果，但電腦很容易做呢個事就只係一個 loop，所以就有咗一個不對等嘅機會。

最常見嘅最鏈關係就係 recurrence relation，好似 Fibonacci number 咁，用前兩個 values 推後一個，再推後一個咁。

所以呢題個思路就係將呢個 problem 需要嘅 sequence，變成一個用 recurrence relation define 嘅 sequence，然後讀者就會好難明段 code 點 work。

個 problem 需要咩 sequence 呢？

呢個 sequence

4 個 spaces，1 粒星，3 個 spaces，2 粒星 ⋯

Turn out 呢個 sequence 唔係太容易 design，我搵咗佢嘅 finite difference

```
4 - 1 = 3
1 - 3 = -2
3 - 2 = 1
...
```

如果你唔理正負號，呢個就係一個 `3,2,1,...` 而已，而正負號亦很簡單，只係正負正負咁跳。

跟住我引用咗一個我以前證咗嘅一個結果，如果我有一個 recurrence relation 係咁 define

```
a(n) = -2a(n-1) - a(n-2)
```

呢個 sequence 嘅 solution 就會係一個 alternating sign arithmetic sequence。個 arithmetic sequence 嘅 coefficient 可以隨便用 initial value 定，所以我可以設計咗 `n`,`o`,`t` 嘅 formula 同 `n`, `o` 的 initialization。

其它其實就好容易解釋了，利用`y`去睇係單數定雙數步，用`h`去將 finite difference 變返做 running sum 等等。

淨返落嚟就係玩埋 variable naming了，留意個 function name 任我揀，variable initialization order 任我揀，但 while loop 個 `w` 我冇得揀 (我或者可以揀 `for` 個 `f`)，然後下面個 loop body 我個自由度就低好多，我可以選擇 print 先定 update 先，但其它基本上唔郁得，所以我 search 字典搵可能性，就搵到呢對。

anyhow 同 python 呢兩個字連埋根本冇咩意思，又唔應節，只係我冇得揀嘅結果。我諗我用多啲時間去 explore 個字典，可能會搵到更好嘅。

就 obfuscation 效果嚟講，呢個作品其實同 hello-world 差，比 Cecilia 都差 ⋯ 呢兩篇嘅數學原理都比呢篇深入。但呢題有一個優點，就係佢 parametrizable，輸入唔同嘅 `i`，可以 output 唔同嘅結果，相反上兩題都有用問題 input 嘅特性嚟做文章，呢篇係冇。

只要你肯 debug，其實唔難發現 alternating arithmetic sequence，尤其係只係 `3,-2,1,0, ...`，上次加咗 `mod 11` 先至 debug 到你暈。呢次個 obfuscation 就係呢個位唔太正。

hello-world 係呢篇：https://obfuscatedanswerstoprogrammingquestions.quora.com/In-the-spirit-of-the-April-Fools-day-let-me-share-this-interesting-piece-of-code-This-will-print-you-fool-10-times

Cecilia 係呢篇：https://obfuscatedanswerstoprogrammingquestions.quora.com/https-www-quora-com-What-is-the-algorithm-to-find-the-average-of-the-numbers-1-to-10-answer-Cecilia-Chen-45

仲有呢篇 abc：https://obfuscatedanswerstoprogrammingquestions.quora.com/https-www-quora-com-How-do-I-write-an-algorithm-that-calculate-the-sum-and-average-of-all-even-numbers-between-1-and-5