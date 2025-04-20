# 1stApril

又到咗一年一度嘅愚人節，自然係要搵啲嘢玩，今次寫咗呢個 program 。

```py
code=[98,89,43,188,138,171,223,223]
print("".join([chr(i%281)for i in[sum(code[j]*89**(j*k)for j in range(8))for k in range(8)]]))
```

個 program 會 output `1stApril`。

當然，完全唔 clear 呢段 code 係點 work，呢篇文就係用嚟解釋下佢點樣運作。

# Discrete Fourier Transform

講呢段 code 嘅創作理念，需要先講返 Discrete Fourier Transform 嘅概念。

Discrete Fourier Transform 係一個 signal processing 好常用嘅 algorithm。

可以咁睇 $X(f) = \sum\limits_{t=0}^{n-1}x(t)\omega^{-ft}$ ，其中 $\omega = e^{\frac{2\pi i}{n}}$ 係一個 primitive $n$ -th root of unity。 $f$ 可以係由 0 去到 $n-1$ 嘅 integers。

條 formula 可以咁睇， $X(f)$ 其實係 $x$ 同 $\Omega_f = (\omega^0, \omega^f, \cdots, \omega^{(n-1)f})$ 嘅 inner product。

> 注意，當我哋係 complex vector space 度做 inner product 嘅時候，係要 conjugate 其中一個 vector 嘅。要 conjugate 一個 vector 係因為我哋唔想有一個非零嘅 vector 同自己嘅 inner product 係 0 (i.e. orthogonal to itself)。如果冇呢個 conjugation，(1, i) 呢個 vector 同自己嘅 inner product 係 0，唔好。

所以呢啲 $\Omega_f$ 好有趣，我哋研究下佢哋有咩性質。

首先 $\Omega_0$ 好無聊，佢只係一個 $n$ 個 1 嘅 vector。

出於好奇，我哋計下 $\Omega_p$ 同 $\Omega_q$ 嘅 inner product，假設 $p \ne q$ 。

$$
\begin{align*}
& \sum\limits_{t=0}^{n-1}\omega^{pt}\omega^{-qt} \\
=& \sum\limits_{t=0}^{n-1}\omega^{(p-q)t}  \\
=& \frac{\omega^{(p-q)n} - 1}{\omega^{p-q} - 1} \\
=& 0
\end{align*}
$$

其中我哋用咗 $\omega^n = 1$ 同埋 $\omega^{p - q} \ne 1$ 呢兩個 fact。

所以其實呢啲 $\Omega_f$ 係 orthogonal 嘅。

然後做埋同自己個 inner product：

$$
\begin{align*}
& \sum\limits_{t=0}^{n-1}\omega^{ft}\omega^{-ft} \\
=& \sum\limits_{t=0}^{n-1}1  \\
=& n
\end{align*}
$$

所以其實除咗多咗 $n$ 倍之外，Discrete Fourier Transform 係一個 orthogonal projection，或者話係一個 change of basis，係冇 lost 咗任何 information。

# Inverse Discrete Fourier Transform

用上述嘅 formula，唔難計到 inverse 嘅 formula：

$$
\begin{align*}
& \sum\limits_{f=0}^{n-1}X(f)\omega^{fi} \\
=& \sum\limits_{f=0}^{n-1}\left(\sum\limits_{t=0}^{n-1}x(t)\omega^{-ft}\right)\omega^{fi} \\
=& \sum\limits_{t=0}^{n-1}x(t)\left(\sum\limits_{f=0}^{n-1}\omega^{-ft}\omega^{fi}\right) \\
=& n x(i)
\end{align*}
$$

# Number Theoretic Transform
留意上述嘅證明其實只係用咗 $\omega^n = 1$ 同埋 $\omega^{p - q} \ne 1$ 呢兩個 facts，所以如果我哋去咗一個 finite field，搵到一個有同樣 property 嘅 $\omega$ ，其實一樣可以做 Fourier Transform。

咁做 Fourier Transform 會冇咗 signal processing 嘅意義，但係我哋依然可以利用 Fast Fourier Transform 嘅 algorithm 去做 speed up。而且 integer 係冇 precision 嘅問題。

前題係 ⋯ 有呢個 primitive root of unity。同埋個 finite field 要足夠大，可以 encode 到需要嘅 information。

## Design

我想 encode 嘅 data 係 ASCII character，所以個 finite field 最少要有 256 個 elements。

另外一點係，我哋知道 finite field 係 cyclic，所以一個 finite field 如果有 $p - 1$ 個 elements，就一定有一個 primitive $p - 1$ -th root of unity。

我哋要 encode 嗰條 string 嘅 length 係 8，所以我要搵一個 prime，佢 $\ge 256$ ，同埋 $p - 1$ 係 8 嘅倍數，我揀咗 281。

3 係 $\mathbb{Z}_{281}$ 嘅 primitive root，所以 $3^{\frac{280}{8}} = 60 \mod 281$ ，就係一個 primitive 8-th root of unity。

> 我記得唔存在一個正嘅 algorithm 去搵呢個 primitive root of unity，只能 brute force，但好似有啲奇怪嘅 bound 話呢個 brute force 成功率好高，所以 expected runtime 係 linear with $n$ ，咩 Riemann hypothesis 咁，嗰堆數我唔明 ⋯ 呢個 3 係 brute force 試出嚟嘅。

我搵埋呢兩個 multiplicative inverses：

 $60 \times 89 \equiv 1 \mod 281$ 同埋 $8 \times 246 \equiv 1 \mod 281$ 

由於 inverse transform 要除 $n$ （即係乘 multiplicative inverse），所以為咗簡化個 puzzle，嗰條 "1stApril" 嘅 string 係用 $60$ 去做 inverse transform，得出個嗰 array 用 $89$ 去做 transform 就可以 revert 返了。


```py
# Design
code = [ord(c) for c in "2ndApril"]
code = [i*246%281 for i in[sum(code[j]*60**(j*k)for j in range(8))for k in range(8)]]

# Challenge
print("code=%s" % code)
print("".join([chr(i%281)for i in[sum(code[j]*89**(j*k)for j in range(8))for k in range(8)]]))
```


當然，我可以用上述方法搵其實合適嘅 finite field 同 primitive root of unity 去做唔同 length 嘅 string，例如咁：

```py
def is_prime(num):
    if num <= 1:
        return False
    for i in range(2, int(num**0.5) + 1):
        if num % i == 0:
            return False
    return True

def next_prime(start):
    num = start + 1
    while not is_prime(num):
        num += 1
    return num

def mod_inverse(a, m):
    m0, x0, x1 = m, 0, 1
    if m == 1:
        return 0
    while a > 1:
        q = a // m
        m, a = a % m, m
        x0, x1 = x1 - q * x0, x0
    if x1 < 0:
        x1 += m0
    return x1

def factors(x):
    result = set()
    for i in range(2, int(x**0.5) + 1):
        if x % i == 0:
            result.add(i)
            result.add(x // i)
    return result

def find_generator(p):
    phi = p - 1
    factors_of_phi = factors(phi)
    for g in range(2, p):
        is_generator = True
        for factor in factors_of_phi:
            if pow(g, phi // factor, p) == 1:
                is_generator = False
                break
        if is_generator:
            return g
    return None

def compute_values(s):
    # Compute the length of the string
    n = len(s)
    code = [ord(c) for c in s]
    
    # Find the smallest prime p > max(code) such that p - 1 is a multiple of n
    p = next_prime(max(code))
    while (p - 1) % n != 0:
        p = next_prime(p)

    # Compute the generator g of the finite field mod p
    g = find_generator(p)
    if g is None:
        raise ValueError("No generator found for the finite field mod p.")

    # Compute e = g^((p - 1)/n) mod p
    e = pow(g, (p - 1) // n, p)
    
    # Compute d to be the multiplicative inverse of e mod p
    d = mod_inverse(e, p)
    
    # Compute i to be the multiplicative inverse of n mod p
    i = mod_inverse(n, p)

    # Compute the code
    code = [v * i % p for v in[sum(code[j]*e**(j*k)for j in range(n))for k in range(n)]]

    return code, p, d, n


# Design
s = "2nd April 2025"
code, p, d, n = compute_values(s)


# Challenge
print("code=%s" % code)
print("print(\"\".join([chr(v%%%s)for v in[sum(code[j]*%s**(j*k)for j in range(%s))for k in range(%s)]]))" % (p, d, n, n))

# Testing
print("".join([chr(v%p)for v in[sum(code[j]*d**(j*k)for j in range(n))for k in range(n)]]))
```
[Try it online!](https://tio.run/##lVRRb9owEH7Pr7hRIdmMUei0l6lMmrr1cU/TXhhDHjHBbeJktsPor2d3tlMMDdOGWkjufPfd992dmye3rfXbN81T83Q45HIDyq4aoyrJdFvx9xngR20AX@B2DrNgoI@RrjUa7kVppTduagMKlAYjdCHZzRifHWUZjaaTdxxew4wfw2POIYbM5zA9OnpzR8NX08osoyq13LtYp3XCuJiZUs7BWwjP235vVSlB166PWhf0eh5PRyS0BaCqzldK76SxkokxdHHVdAx7@p8hXjUGfJx1WlXE6KVS06QaAR/SE78wiYDra6ieTZhTeOuQHo/2Z1D8eoOBo2CZduBovk3lxHfkVk1TcvtZ4LYRa1cby/aRlJG2LR0JKB3jf2nq/lJL9xcbSpknIs@Z4pc8e1IgumOlwRurVTpfFVJLI7Bq1kTkZouA0KAYoQGR1KrerIKrY4lvR07FCacmZWGPIBjtR67zUWRIR@GnSKeEUYum/s2Ksa8PeYXDBHU2HRdwj7Offn4aKR5TwdOo3h0qUjm/1Druz7qumtbJ1U6UrbTMRgGu4C44wG0llFIXbgv1xr9ZZ5QO6TQWiE4M86/rOpdoWdQmZ2vuVVqTQHbp3THzPfYvJKpEWUrrwC8jtu4DVGLPKAsH2663eEq40FIkiDtQ4RCoBvcGS9Gh64iX3AHP8TxZMuYzcBxJDa9ORvIsuuFZD/tjN4pOAhxB5ehHljndDND4uIKm7Hw6u4UsiAIJn1wIQlkJ30j6z8bg6cGXOoHb1C0qRSr2Y04GL@ol@YsfLDK@1jypjnxxFjtFcB41jWLani4V9qiGn0GCqLtaC6d2EuI9SGrIBCCnKzC5JuWl1OqfUusktTpLHaruaRY1/2QWd3gzKmx944XcIcDCtpUfksXDciRHI/YweuTkfDjeBZp7y2NqWS6zdIcoA1YxhnyMs5hlV/BJWlXozCLs4AY79xGnqoSb6c27QXZyGg@82Duf4W5LG4FwGYbi/TqgsPnQDpCAH@vOHn6@D74PJg@10myx3hq2Gw6HlvfSHNp@nnj@nCialkvOCZLFevEPFwrr@4rbSrsfqzgFb/qh8/9RmPPD4Q8 "Python 3 (PyPy) – Try It Online")