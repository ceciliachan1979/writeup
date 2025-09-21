# abc

呢條係一條 2023 年嘅作品，目標係計 1 到 50 裡面所有偶數嘅平均數

```py
[a,b,c]=[0,1,2] 
for i in range(0,24): 
 a=a*2 
 a=a+b 
 a=a%5 
 b=a+b 
 b=b%5 
 c=c+(5*a+b+1)*2 
print("The sum is %s" % c) 
print("The average is %s" % (c/25.0)) 
```

比較明顯嘅係 `c` 係 accumulator，它把 `a` 和 `b` 當成 5 進位數去理解，然後 (ab + 1) 再做加總。

不明顯嘅係點解 `ab` 會遍歴晒 `00` 到 `44`。 

如果我們 debug 一下，喺個 loop 度 print 啲 a 同 b 出嚟，就會睇到個 loop 的確遍歴晒 `00` 到 `44`，只係個 order 亂晒。 

呢題如果我冇留 hint 俾自己，我諗我 reverse 唔到。

設 $\hat{a}$ 和 $\hat{b}$ 為 loop body 後 $a$ 和 $b$ 的值，唔難睇到

$$\begin{align*}
\hat{a} &= 2a + b \pmod{5} \\
\hat{b} &= 2a + 2b \pmod{5}
\end{align*}$$

要理解點解呢兩條式可以 loop 晒 `00` -> `44`，就要由 Galois field 講起。

我哋諗 `(a,b)` 其實係 $ax + b$ 呢條 polynomial，然後我哋考慮 $(ax + b)(x + 2) \pmod{x^2 - 2}$

$$\begin{align*}
   & (ax+b)(x + 2) - a(x^2 - 2)\\
  =& ax^2 + (2a + b)x + 2b - a(x^2 - 2) \\
  =& (2a + b)x + 2a + 2b
\end{align*}$$

呢個就係 $(a,b) \to (\hat{a}, \hat{b})$ 個設計，我係計緊 $(x+2)$ 嘅 power mod $x^2-2$。

咁點解 $(x+2)$ 嘅 power mod $x^2 - 2$ 可以 loop 晒 `00` 到 `44`呢？呢個就係 Galois field 理論了。$x+2$ 係 GF($5^2$) 嘅 multiplicative group 嘅 generator，所以佢得。

呢個 post 主要寫 obfuscation 方法，數學原理就唔寫太多了。

這段代碼第一次在 [Quora](https://obfuscatedanswerstoprogrammingquestions.quora.com/https-www-quora-com-How-do-I-write-an-algorithm-that-calculate-the-sum-and-average-of-all-even-numbers-between-1-and-5) 發佈。

[Try it online!](https://tio.run/#%23TYqxDsIgFEV3vuKlCQkUohRlMeEv3JoOD4Itg7SBauLXY62DTifnnru81mlOp1p7lE76wfZKdlIP5DZniBATZExjYErqM78QQIut3iHcDmoIuK856z7mrRfMtNskOr6dlxzTyprrFKA87hAL0NIABc%2F%2FEz5DxjH8MvNHbQ6K81rf "Python 3 (PyPy) – Try It Online")