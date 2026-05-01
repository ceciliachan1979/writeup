# Help!

先睇段 code：

```py
a = 38778728451359569425447045333393144816584556838353140550008773
b = 137125379776767170179580765213112542081894655469464622108508160
for c in range(90):
  a *= 256
  d, a = divmod(a, b)
  print(chr(d), end='')
```

Output：

```txt
Help! I am stuck in a loop I am stuck in a loop I am stuck in a loop I am stuck in a loop I am s
```

[Try it online!](https://tio.run/##FY47bsNADER7nYKdpUAFufxuAB1G9jqxiqwMwTCQ0yvMsBm8Acl5/r4ee@fzXGEBDvfwEqLEWtWqFBVxFOVUZRIJMs1cLThYk6AqIuYiD9c8QexUlL26Ww45klcNdNNCTJlJwaCoYqqSH0ysFMLQpIbD137ADbYOx9q/72PF6XMAWOFjgaKWts3wX7Vt75@9jesM1ynp89j6a7w9jrFNM9x7Wy6XaTjPPw)

兩個大數，一個 loop，就可以 print 出一段不斷重複嘅 text。點解呢？

# 循環小數

要理解呢段 code，首先要明白循環小數同分數嘅關係。

喺十進位入面，大家都知道 $\frac{1}{3} = 0.\overline{3}$ ，又或者 $\frac{1}{7} = 0.\overline{142857}$ 。

其實任何分數都必然係 finite 或者 repeating，以下係一個簡短嘅證明。

**Problem**：設 $n$ 為整數且 $\gcd(n, 10) = 1$ （即 $n$ 不被 2 或 5 整除），證明存在 $m$ 使得 $nm = \underbrace{99\ldots9}_{k}$ 。

**Proof**：By Euler's theorem， $10^{\phi(n)} \equiv 1 \pmod{n}$ ，所以 $\underbrace{99\ldots9}\_{\phi(n)} = 10^{\phi(n)} - 1 \equiv 0 \pmod{n}$ 。即 $\underbrace{99\ldots9}\_{\phi(n)} = mn$ 。

**Corollary**：任何整數 $n$ 都可以寫成 $mn = \underbrace{99\ldots9}\_{k}\underbrace{00\ldots0}\_{j}$ 或 $mn = 10^j$ （先處理 2 和 5 嘅 factor，剩低嘅用上面嘅 result）。

**Corollary**：任何分數嘅十進位展開都係 finite 或 repeating。

反過嚟，如果我有一個純循環小數 $0.\overline{R}$ ，其中 $R$ 有 $r$ 個 digit，佢嘅分數就係：

$$
0.\overline{R} = \frac{R}{\underbrace{99\ldots9}_{r}} = \frac{R}{10^r - 1}
$$

例如 $0.\overline{142857} = \frac{142857}{999999} = \frac{1}{7}$ 。

如果有 prefix 呢？例如 $0.1\overline{23}$ ：

$$
0.1\overline{23} = \frac{123 - 1}{990} = \frac{122}{990} = \frac{61}{495}
$$

一般嚟講，如果個 prefix $P$ 有 $p$ 個 digit，repeating part $R$ 有 $r$ 個 digit：

$$
0.\underbrace{P \ldots P}_{p}\overline{\underbrace{R \ldots R}_{r}} = \frac{P \cdot (10^r - 1) + R}{10^{p+r} - 10^p}
$$

呢個公式其實好直觀。分母 $10^{p+r} - 10^p$ 就係 $\underbrace{99\ldots9}\_{r}\underbrace{00\ldots0}\_{p}$ ，前面嗰啲 9 負責 repeating，後面嗰啲 0 負責 shift 個 prefix 去啱嘅位。

# 由十進位到十六進位

上面嘅公式用十進位，但其實任何 base 都一樣 work。如果我哋用 base 16（hexadecimal），條公式變成：

$$
\frac{P \cdot (16^r - 1) + R}{16^{p+r} - 16^p}
$$

# 由 Text 到 Hex

每一個 ASCII character 嘅 hex representation 都係 2 個 hex digit。例如 `Y` 嘅 ASCII 係 89，即係 hex `59`。

所以一段 text 可以直接轉成一串 hex string，然後呢串 hex string 就可以當成一個大數。

- `"Help! "` → `48656c702120`
- `"I am stuck in a loop "` → `4920616d20737475636b20696e2061206c6f6f702`

有咗呢兩個 hex string，我哋就可以用上面嘅公式 construct 一個分數，佢嘅 hexadecimal expansion 就係 prefix 跟住無限重複嘅 repeating part。

# Decoding

有咗個分數 $\frac{a}{b}$ 之後，點樣 decode 返出 text 呢？

```py
a *= 256
d, a = divmod(a, b)
print(chr(d), end='')
```

呢個就係 long division in base 256。

256 即係 $16^2$ ，即係兩個 hex digit，即係一個 byte，即係一個 character。

每一步乘 256 就等於將個小數點右移一個 character 嘅位，然後 `divmod` 攞商同餘數。商就係下一個 character 嘅 ASCII value，餘數就係剩低嘅小數部分，留返俾下一次用。

呢個同你手動做長除法完全一樣，只不過係 base 256。

# Constructor

以下係 construct 呢個分數嘅完整 code：

```py
def text_to_hex_fraction(prefix, repeat):
    def text_to_hex(text):
        return ''.join(f'{ord(c):02x}' for c in text)
    
    prefix_hex = text_to_hex(prefix) if prefix else ""
    repeat_hex = text_to_hex(repeat)
    
    p_len = len(prefix_hex)
    r_len = len(repeat_hex)
    
    P = int(prefix_hex, 16) if prefix_hex else 0
    R = int(repeat_hex, 16)
    
    numerator = P * (16**r_len) + R - P
    denominator = 16**(p_len + r_len) - 16**p_len
    
    from math import gcd
    g = gcd(numerator, denominator)
    
    return numerator // g, denominator // g
```

留意 `numerator = P * (16**r_len) + R - P` 其實就係 $P \cdot (16^r - 1) + R$ ，同上面嘅公式一樣。最後用 GCD simplify 個分數。

用法好簡單：

```py
a, b = text_to_hex_fraction("Help! ", "I am stuck in a loop ")
print(a, b)
for c in range(90):
  a *= 256
  d, a = divmod(a, b)
  print(chr(d), end='')
```

[Try it online!](https://tio.run/##bVLLboMwELzzFVMu2IQ0j6qRGol7e4v6A5ELBtyCjRynoqr67altrECq@mAtuzOzw3r7L9Mo@XC5lLyC4YM5GnVs@HCsNCuMUJL0mldiyKB5z5mh@wj2/EMETF4eSO5qbs5ZIkvt3JSSpkm@lS1LQ/Xo7/CSolEYBIb0E9Sx/ja2cIPIb@bFAIaqAAW9PHHEcjd2cs39owfKswbHl0oLsTaZmI0DPapPijHywRSHNjJhhs5uZ8g68sbUnvAbCpOYJk6I8d1wzY6eRW/UUZLNLU@@DYmHpSxzCtKXqhAxIByLjnywQ0Euf9clJvtKqQ8dMA9H1ShvURekLtVWxMbn2z@YtZgbDO04@VyvUN2CfiSKW4e12@NMCxc@87e8QZ4hfwDqczLn4cK/P0CrVI6ZRr92cnAiNrsuhmaw5eVr7vWJIc2wfdzYsM/uVoxSfnSoDye2OkygaTUqagcsyTxIaXS6/)

# 討論

呢個 obfuscation 嘅有趣之處在於，最終嘅 code 非常簡單——兩個大數加一個 loop——但背後嘅原理其實就係大家小學都學過嘅循環小數。

同其它 obfuscation 唔同（例如 [1st April](1st-april.md) 用 NTT，[You fool](you-fool.md) 用 continued fraction），呢個方法嘅數學非常 elementary，但 obfuscation 效果一樣好，因為啲大數本身就足夠 confusing。

另外一個有趣嘅 observation：如果 prefix 係空嘅，個分數就會更加簡潔。例如：

```py
a, b = text_to_hex_fraction("", "Hello! ")
```

咁出嚟嘅分數分母就會係 $16^r - 1$ 嘅形式，即係一堆 `f` 嘅 hex number，呢個同十進位嘅 $\frac{R}{999\ldots9}$ 完全一樣。

呢個方法嘅限制係佢只可以 encode repeating 嘅 text（可以有一個 non-repeating prefix），但呢個限制反而令到啲 output 更加有 obfuscation 嘅 feel——因為 repeating text 本身就有一種 taunting 嘅效果 😈。
