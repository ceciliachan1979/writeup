# poly

2025 年新作品，點樣唔用乘號計 polynomial evaluation。

```Java
public class Main { 
  public static void main(String[] args) { 
    int a = 6, b = 9, c = 8, x = 3; 
    for (int i = 0; i < x; i++) { 
      a += b; 
      b += c; 
    } 
    System.out.println("f(" + x + ") = " + a); 
  } 
} 
```

[Try it online!](https://tio.run%2F%23%23NY7BDoIwEETvfsWEU0mRmJgYDfIJnjgaD6UIKUIhtBCM4dvrFvGyL7M7O7u1mMS@tio.run/#%23NY7BDoIwEETvfsWEU0mRmJgYDfIJnjgaD6UIKUIhtBCM4dvrFvGyL7M7O7u1mMS%40Ll7O9WPeKAnZCGNwE0rjswO2rrHCEqZOFWhpxjI7KF3dHxBDZcLVCihtIZDiFCEnXCJIwjnCTDgmq6fsBjBvVNQ7JIQrZgLn%2FxRQBk%40RJ5vKvZI%2Ftaw1exv7bONutHFPb9hGs6BkATgd4ghCSvZChH5p2S3OfQE "Python 3 (PyPy) – Try It Online")

神奇嘅地方當然係計 polynomial 但竟然冇乘號 ⋯ 當然，個 loop 係用 repeated 嘅加數去做到乘數嘅效果，但係唔明顯點解咁加就可以。

> 當然，用 repeated addition 去代替乘法係會成 $O(xd)$ 咁慢啦。 $x$ 係要 evaluate 個 point 而 $d$ 係 polynomial degree。 (pun intended 😆 XD)

想明白點整出嚟，我哋可以考慮一個 finite difference scheme：

$$
\begin{align*}
a(n) &= 4n^2 + 5n + 6 \\
b(n) &= a(n+1) - a(n) \\
     &= 4(n+1)^2 + 5(n+1) + 6 - (4n^2 + 5n + 6) \\
     &= 8n + 9 \\
c(n) &= b(n+1) - b(n) \\
     &= 8(n+1) + 9 - (8n + 9) \\
     &= 8
\end{align*}
$$

有了這個 finite difference scheme 後，算 $a(n)$ 和 $b(n)$ 就可以這樣算了：

$$
\begin{align*}
a(n + 1) &= a(n) + b(n) \\
b(n + 1) &= b(n) + c(n) 
\end{align*}
$$

當然，我們要有 $a(0),b(0),c(0)$ 的值，然後一直的往上加就可以了。

當然，這個方法的關鍵係 $c(n) = 8$ 係一個 constant。咁係咪係 polynomial 最終都會變成 constant 呢？係！

呢個方法一定會每次將個 polynomial 嘅 degree decrease by exactly 1。所以最終一定會係 constant，我哋可以試吓唔同嘅 polynomial可以出到咩結果。

```py
# A generator for the polynomial evaluation code

coeff = [4,5,6]
n = 3

#
# Compute the finite difference table
#
degree = len(coeff) - 1
a = []
for x in range(degree + 1):
  v = 0
  for c in coeff:
    v *= x
    v += c
  a.append(v)
table = [a]
while len(table[-1]) > 1:
  b = []
  for i in range(len(table[-1]) - 1):
    b.append(table[-1][i + 1] - table[-1][i])
  table.append(b)
vector = [row[0] for row in table]
v = len(vector)

#
# Generate the code
#
code = []
code.append("(%s) = (%s)" % (",".join(["v%s" % n for n in range(v)]), ",".join([str(i) for i in vector])))
code.append("for i in range(%s):" % n)
for v in range(v - 1):
  code.append("  v%s += v%s" % (v, v + 1))
code.append("print(v0)")
code.append("print(%s)" % " + ".join(["%s * %s ** %s" % (c, n, degree - i) for (i, c) in enumerate(coeff)]))
code = "\n".join(code)

#
# Evaluate it
#
print(code)
exec(code)
```
[Try it online!](https://tio.run/##bVPBjtowEL37K0ZBSPZusgJt28NKVKqqqh@R@mDMBFwFOwrBC19PZ2wDu6iHxJOZ53nPb5zhPO2Cf22G83C@XGbwA7bocTRTGKGjZ9ohDKE/@7B3pgeMpj@ayQUPNmxQCBuw62AF7Zf6a/1NC0/xqxAzMYOfYT8cJ0wtOucdhRvXdTiit5Q16x4Jt8HtiEi7evQydVPQwFIYbqoFaziB8zAav0VZ0M@wVG8CIBJoQSujLKNSA65w7WkFpxI@r8BSaF7MMKDfyKhEEsAkRov3naOYFaRs2yy1gu@w5E7rLCSTuLuUB3RTJNGGK8mt2jpWrAnzIaUVodP3Fb9WIqJl54lxDO/tQidSCpk2YbWIxasMVdnr33lo2ew0mZngJWvn6EpSyflBUZqXCuYgq7p6@Rucl20V5wdO@cTq70eNSqsa7sDDNEqn7oZkKVop9ZnqwTFifEv9VRpr/EBws@/Tfprc/MCzK8JkrHmWBH0gGkbnJxkXqvpvoRy1oq23s1LjJ@AXv1NzW4OvodywBsoBpavBKpaK/rhPJpdrqosKMrP640tjTpSZ/Mr/CoKb6DsryWU8oc3h5fIP "Python 3 (PyPy) – Try It Online")

接下來的部分其實同段 code 冇關，主要係嘗試 solve 個 program 究竟係度計緊乜。想像你要去 reverse engineering 呢段 code，你會想去 solve 呢個 recurrence。

$$
\begin{align*}
  a(n+1) &= a(n) + b(n) \\
  b(n+1) &= b(n) + 6 
\end{align*}
$$

又或者用 matrix notation

$$
\begin{align*}
  \left(
  \begin{array}{c}
  a_{n+1} \\
  b_{n+1} \\\
  c_{n+1}
  \end{array}
  \right) &= \left(
  \begin{array}{ccc}
  1 & 1 & 0 \\
  0 & 1 & 1 \\
  0 & 0 & 1 \\
  \end{array}
  \right) \left(
  \begin{array}{c}
  a_n \\
  b_n \\\
  c_n
  \end{array}
  \right)
\end{align*}
$$

換句話說，我們想找中間呢個 matrix 的 power。

呢個 matrix 係 Jordan form，唔可以被 diagonalize。呢個其實係 expected，因為我哋明知個答案係 polynomial，佢冇可能有個 $n$ 喺個 index 度。

要搵 Jordan form 嘅 power，個 matrix 可以咁樣拆：

$$
\begin{align*}
   &\left(
  \begin{array}{ccc}
  1 & 1 & 0 \\
  0 & 1 & 1 \\
  0 & 0 & 1 \\
  \end{array}
  \right)^n \\
  =& \left(\left(
  \begin{array}{ccc}
  1 & 0 & 0 \\
  0 & 1 & 0 \\
  0 & 0 & 1 \\
  \end{array}
  \right)+\left(
  \begin{array}{ccc}
  0 & 1 & 0 \\
  0 & 0 & 1 \\
  0 & 0 & 0 \\
  \end{array}
  \right)\right)^n
\end{align*}
$$

第一個 matrix 只係 identity，佢嘅所有 power 都係 identity。
第二個 matrix 叫做 shift matrix，佢個功能係 shift 向左，即係 f([1,2,3]) = [2,3,0] 咁，咁好自然 shift shift 吓就會 0 晒。呢種 repeated application 最終會 0 晒嘅 matrix 我哋叫做 nilpotent。重點係呢個 matrix 只有 finite 咁多個 power，所以如果我哋用 binomial theorem 去爆個 power expansion，無論 n 幾大，最終都會只有 finite 個 term，呢個就係計 Jordan form power 嘅方法。

咁計嘅話，最終你會得到一個 linear combination of binomial coefficient，然後計得返原本條 polynomial ⋯ 

$$
\begin{align*}
   &\left(\left(
  \begin{array}{ccc}
  1 & 0 & 0 \\
  0 & 1 & 0 \\
  0 & 0 & 1 \\
  \end{array}
  \right)+\left(
  \begin{array}{ccc}
  0 & 1 & 0 \\
  0 & 0 & 1 \\
  0 & 0 & 0 \\
  \end{array}
  \right)\right)^n \\
  =& \left(
  \begin{array}{ccc}
  1 & 0 & 0 \\
  0 & 1 & 0 \\
  0 & 0 & 1 \\
  \end{array}
  \right) + C^n_1
  \left(\begin{array}{ccc}
  0 & 1 & 0 \\
  0 & 0 & 1 \\
  0 & 0 & 0 \\
  \end{array}
  \right) + C^n_2
  \left(\begin{array}{ccc}
  0 & 0 & 1 \\
  0 & 0 & 0 \\
  0 & 0 & 0 \\
  \end{array}
  \right)
\end{align*}
$$

所以

$$
\begin{align*}
   & a(n) \\
  =& a(0) + C^n_1b(0) + C^n_2 c(0) \\
  =& 6 + 9n + 8\frac{n(n-1)}{2}    \\
  =& 4n^2 + 5n + 6
\end{align*}
$$

Phew ...

這段代碼第一次在 [Quora](https://obfuscatedanswerstoprogrammingquestions.quora.com/Recently-stumbled-upon-a-technique-to-hide-a-polynomial-evaluation-https-www-quora-com-How-do-I-solve-a-polynomial-i) 發佈。

