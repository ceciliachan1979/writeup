# Answer

**Creation time:** Sep 18, 2025 02:35 PM PDT

**Content language:** English

## Question

How do I prove that $(10^{n+1}-9n-10)/81$ is an integer by mathematical induction?

## Content

To start with, we need a bit of preparation.

$$
\begin{aligned}
& 10^{n+1} \\
={} & 10(10^n) \\
={} & (11-1)(10^n) \\
={} & 11(10^n) - 10^n \\
={} & 11(10^n) - 10(10^{n-1}) \\
={} & 11(10^{(n-1)+1}) - 10(10^{(n-2)+1})
\end{aligned}
$$

and also

$$
\begin{aligned}
& -9n - 10 \\
={} & -99n + 90n - 11 + 1 \\
={} & -99n + 90n - 11 - 80 + 81 \\
={} & -99n - 11 + 90n - 80 + 81 \\
={} & 11(-9n - 1) - 10(-9n + 8) + 81 \\
={} & 11(-9n + 9 - 10) - 10(-9n + 18 - 10) + 81 \\
={} & 11(-9(n-1) - 10) - 10(-9(n-2) - 10) + 81
\end{aligned}
$$

All we did is just some simple algebraic manipulation, now we put them together and magic starts to happen.

$$
\begin{aligned}
10^{n+1} - 9n - 10
&= 11(10^{(n-1)+1} - 9(n - 1) - 10) \\
&\quad - 10(10^{(n-2)+1} - 9(n-2) - 10) + 81 \\
\frac{10^{n+1} - 9n - 10}{81}
&= 11\left(\frac{10^{(n-1)+1} - 9(n-1) - 10}{81}\right) \\
&\quad - 10\left(\frac{10^{(n-2)+1} - 9(n-2) - 10}{81}\right) + 1
\end{aligned}
$$

Suppose we denote $a\_n = \frac{10^{n+1} - 9n - 10}{81}$, we have $a\_n = 11a\_{n-1} - 10 a\_{n-2} + 1$ where $a\_0 = 0$ and $a\_1 = 1$.

Now it is clear that we wanted to prove $a_n$ are integers, and we will prove that with mathematical induction, as required.

Let $P(n)$ be the statement $a_n$ is an integer. It is clear that $P(0)$ and $P(1)$ are true by definition.

For $k \ge 2$, suppose $P(i)$ are true for all $0 \le i < k$, we have

$$
a_k = 11a_{k-1} - 10 a_{k-2} + 1
$$

Now since $a\_{k-1}$ and $a\_{k-2}$ are integers, so is $a\_k$, and we have just proved $P(k)$ is true.

Therefore by the principle of mathematical induction $P(k)$ is true for all $k \in \mathbb{N}$.

In other words,

$$
\frac{10^{n+1} - 9n - 10}{81} \in \mathbb{Z}, \forall n \in \mathbb{N}
$$
