# Catalan number 同 Dyck prefix

> 呢篇由 Claude 寫，作為我叫佢教我嘅實驗，唔係我自己嘅文字。

用 reflection 數 Catalan number，再用同一個 trick 數 Dyck word 嘅合法 prefix。唔使識 reflection，由零開始講。

## 問題

Dyck word 係 $n$ 個 U（上）同 $n$ 個 D（下）嘅 sequence，而且 **任何位置，D 嘅數量都唔會多過 U**。

畫成 path：由 $(0, 0)$ 出發，U 就向上行一格，D 就向下行一格，要行到 $(2n, 0)$，而且 **永遠唔可以跌到 x 軸下面**，即係唔可以掂到 $y = -1$ 。

## 思路：總數 － 壞 path

唔理限制，所有 path 有 $\binom{2n}{n}$ 條（ $2n$ 步揀 $n$ 步做 U）。

我哋要減走嗰啲掂過 $y = -1$ 嘅「壞 path」。問題係壞 path 好難直接數，所以要用一個 trick，就係 reflection。

## Reflection

攞一條壞 path，搵出佢 **第一次** 掂到 $y = -1$ 嘅位置。由起點行到嗰點為止嘅一段，每步 U 變 D、D 變 U（即係以 $y = -1$ 做鏡，將嗰段反轉）。後面嘅部分唔郁。

例子（ $n = 2$ ）：壞 path 係 `DUUD`，高度係 $0 \to -1 \to 0 \to 1 \to 0$ 。第一步已經掂到 $-1$ ，所以只需要對調第一步，變成 `UUUD`。

```
原本壞 path： DUUD  0 -> -1 -> 0 -> 1 -> 0
反轉第一段： UUUD -2 -> -1 -> 0 -> 1 -> 0
```

反轉之後，起點由 $0$ 變咗 $-2$ （因為起點 $0$ 以 $y = -1$ 做鏡嘅鏡像係 $-2$ ），終點仍然係 $0$ 。

## 點解咁樣數得準？

呢個變換係一一對應：

- 每條壞 path 反轉一次，會得到一條由 $-2$ 去 $0$ 嘅 path。
- 反過來，由 $-2$ 去 $0$ 嘅 path 一定要經過 $-1$ （因為每步只係 $\pm 1$ ，唔可以跳過），所以你可以搵返第一次掂 $-1$ 嘅位置，再反轉返去，得返原本嗰條壞 path。

所以壞 path 嘅數量 = 由 $-2$ 去 $0$ 嘅 path 數量。呢類 path 要淨升 2 格，即係 U 比 D 多 2 個， $2n$ 步入面 U 有 $n + 1$ 個，所以有 $\binom{2n}{n+1}$ 條。

$$
C_n = \binom{2n}{n} - \binom{2n}{n+1} = \frac{1}{n+1}\binom{2n}{n}
$$

驗算 $n = 2$ ： $6 - 4 = 2$ （UUDD、UDUD ✓）。

## 例題：數 Dyck word 嘅合法 prefix

Dyck word 嘅 prefix，就係長度 $m$ 、永遠唔跌穿 x 軸嘅 path，終點高度 $h$ 要 $\ge 0$ ，而且因為只行 $m$ 步、每步 $\pm 1$ ，所以 $h \le m$ ，並且 $h$ 要同 $m$ 同奇偶。（因為最後補 $h$ 個 D 就可以完成做 Dyck word。）

### Step 1：固定終點高度 $h$

令 $k = \frac{m+h}{2}$ 。

- 所有 path：U 有 $k$ 個，共 $\binom{m}{k}$ 條。
- 壞 path 經過 reflection，變成由 $-2$ 去 $h$ ，要淨升 $h + 2$ ，U 有 $k + 1$ 個，共 $\binom{m}{k+1}$ 條。

注意：如果 $k + 1 > m$ ，即係 $h = m$ ，要 $m + 1$ 個 U 但只有 $m$ 步，呢類 path 根本唔存在，壞 path 數量係 0。呢種情況我哋約定 $\binom{m}{m+1} = 0$ ，等個 formula 照用，但真正嘅原因係冇 path，唔係「揀 $m + 1$ 個」呢個動作有意思。

所以終點高度 $h$ 嘅合法 prefix 有：

$$
N(m, h) = \binom{m}{k} - \binom{m}{k+1}
$$

驗算 $m = 2$ ： $h = 0$ → $\binom{2}{1} - \binom{2}{2} = 1$ （UD ✓）； $h = 2$ → $\binom{2}{2} - \binom{2}{3} = 1$ （UU ✓）。

### Step 2：唔理終點，數晒所有 prefix

將所有可能嘅 $h$ （同 $m$ 同奇偶）加埋，即係 $k$ 由 $\lceil m/2 \rceil$ 行到 $m$ 。每一項都係 $\binom{m}{k} - \binom{m}{k+1}$ ，加埋會一個減一個，中間全部抵消（telescoping），淨低頭同尾：頭係 $\binom{m}{\lceil m/2 \rceil}$ ，尾係 $-\binom{m}{m+1}$ 。但尾對應 $h = m$ 嗰項嘅壞 path，呢類 path 根本唔存在（約定 $\binom{m}{m+1} = 0$ ），所以尾消失，淨低頭：

$$
\sum_{h} N(m, h) = \binom{m}{\lceil m/2 \rceil} = \binom{m}{\lfloor m/2 \rfloor}
$$

驗算 $m = 3$ ：UUU、UUD、UDU 共 3 個，而 $\binom{3}{1} = 3$ ✓。

## 總結

長度 $m$ 嘅合法 prefix 有 $\binom{m}{\lfloor m/2 \rfloor}$ 個。成個過程只係靠一個 idea：**將難數嘅壞 path，用 reflection 變成易數嘅 path。**
