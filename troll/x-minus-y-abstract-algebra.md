# Answer

**Creation time:** Jul 21, 2025 01:25 PM PDT

**Content language:** English

## Question

If $x-y=0$, then what does $x+y$ equal to?

## Content

The problem didn’t specify what $x$ and $y$ are - so I will make my assumptions.

Because we used the $+$, $-$ and $=$ sign, the underlying algebraic structure must have these operations.

The simplest (i.e. with least governing axioms) algebraic structure that has these operation I know is a group, written additively.

To make things easier, I also assume the group is Abelian, as per the convention that Abelian group are usually written additively.

I don’t know what can we do with non Abelian group other than brute forcing all the elements in the finite case or otherwise stuck …

Imagine solving for the case of $x$ and $y$ being arbitrary bijection of the integers and $+$ is function composition … I just don’t know how/

$$
x - y = 0 \implies x - y + y = 0 + y \implies x = y.
$$

$$
x + y = x + x
$$

To identify the solutions, now we consider the homomorphism $f(x) = x + x$

It is a homomorphism because

$$
f(x+y) = x + y + x + y = x + x + y + y = f(x) + f(y),
$$

note that Abelian is required for this step.

The solution set is the image of this homomorphism.

Using the group first isomorphism theorem, the image of the homomorphism is isomorphic to the quotient of the group by the kernel.

The kernel is set of group element such that $f(x) = 0$, which means $x + x = 0$. By the first isomorphism theorem, this is necessarily a normal subgroup. (Normality is implied by being Abelian anyway)

It would be interesting to look at some examples.

In the easiest case, let’s consider the additive group of an arbitrary integral domain $F$.

Now to find the kernel, we need to solve $x + x = 0$. We can rewrite this as

$$
1x + 1x = 0 \qquad \text{(multiplicative identity)}
$$

$$
(1+1)x = 0 \qquad \text{(distributive law)}
$$

At this point, I have two interesting possibilities.

Either $1 + 1 = 0$ or $x = 0$.

If neither is true, I have found a pair of zero divisor, that would be a contradiction for an integral domain.

First, consider the simpler case where $1 + 1 = 0$

In this case, $x(1+1) = x + x = 0$, that implies $x + y$ can only be $0$, easy.

Second, we consider the case where $1 + 1 \ne 0$. Since we are in an integral domain, $(1+1)x = 0$ then forces $x = 0$.

Now we have found the kernel to be the trivial group with only the identity, which mean the quotient is simply the whole group.

Needless to say this also applies to any field (e.g. real/complex/rational/finite field) as a field is an integral domain.

But wait! We knew for integers, the answer must be just the even integers, but now we say the answer is all the integers? The thing is - first isomorphism theorem only say that the answer is isomorphic to the quotient. In this case, the even integers does in fact isomorphic to the all the integers, in which $f$ is the isomorphism.

Isomorphic groups only differ in the ways we label the elements. In this case, we are simply relabeling $2$ by $1$, $4$ by $2$, and therefore the even integers can be labeled using their halves.

For arbitrary rings, things get complicated. I can’t use the zero divisor rule anymore. For example, in the ring of integer addition mod $4$, the answers are $\lbrace 0, 2 \rbrace$, which is neither the full ring nor simply $\lbrace 0 \rbrace$. In the ring of integer addition mod $9$, however, the answer is all the ring elements because $2 \times 5 = 1 \pmod{9}$ has a multiplicative inverse. In fact, the whole group rule applies to any ring where $2$ has a multiplicative inverse.

Going more abstract, for arbitrary Abelian group, if it is finite, we can simply brute force the zeroes on the diagonal (or simply record all the values for $x + x$). Otherwise, we will need more information.

The more abstract I go, the less I can use, the less I can say anything about the solution set. That’s the nature of the game …

Welcome to the world of abstract algebra my friends.

I never had any formal training on abstract algebra - this is all based on my personal reading. If there are errors, please point them out, I will be happy to correct them. Thanks.
