# Space Submission

**Space name:** Math Questions

**Creation time:** Sep 11, 2025 11:15 AM PDT

**Content language:** English

## Question

How do I show via mathematical induction that $\dfrac{n^{4}}{2} + \dfrac{n^{3}}{3} + \dfrac{n}{6}$ is a natural number for every $n \in \mathbb{N}$?

## Content

We will start with proving a simpler lemma that the binomial coefficients within Pascal's triangle are positive integers using mathematical induction.

Define $S(n)$ to be the proposition that the nth row of the Pascal triangle are all positive integers.

$S(1)$ is obviously true because it is simply 1, 1

Assume $S(k)$ is true. The boundary values in the $k+1$ th row of the pascal triangle are 1, and each interior value is the sum of two numbers of the previous row. Now since the previous row are composed of only positive integers, so all the numbers in the $k+1$ th row are positive integers as well.

So by the principle of mathematical induction, the proposition $S(n)$ is true for all natural number $n$.

With the lemma proved, consider this identity.

$$
\frac{n^4}{2} + \frac{n^3}{3} + \frac{n}{6} = \binom{n}{1} + 9\binom{n}{2} + 20\binom{n}{3} + 12\binom{n}{4}
$$

We are using the convention that $\binom{n}{k} = 0$ if $k > n$.

The right hand side is a sum of nonnegative integers, so it is a nonnegative integer. For $n \ge 1$, the first term $\binom{n}{1} = n$ is positive, so the sum is positive as well. Thus the expression is a natural number.

QED.
