# Answer

**Creation time:** May 28, 2022 09:06 AM PDT

**Content language:** English

## Question

Can every problem be reduced to another problem?

## Content

Let’s get back to the definition of reduction.

A problem $P$ is Turing-reducible to a problem $Q$ if an algorithm can solve $P$ using an oracle for $Q$. Informally:

If we solved Problem $Q$, we can “easily” solve problem $P$ - easily belongs to the eyes of the beholder - it might mean polynomial time if we are talking about polynomial time reducibility, but it could be something else depending on situation.

Ok - now we have a definition of reduction - here is a cute proof that every problem can be reduced to another problem.

If you have a problem $P$, reduce it to this specific problem $Q(P)$ as follow.

Problem $Q(P)$ - solve Problem $P$ and also determine whether the Riemann Hypothesis is true or false.

If you have a solution $Q(P)$, you can “easily” just take the solution to Problem $P$ and consider Problem $P$ solved.

I think we can agree, in all senses of “easily”, taking a solution of Problem $P$ from the solution of $Q(P)$ and consider Problem $P$ solved is considered “easily”.

> Technically, to prove Turing reducibility, we need to provide a machine $M$ that decides membership in $P$ using oracle membership queries to $Q(P)$. Here, $(a,b) \in Q(P)$ exactly when $a \in P$ and $b$ is the correct Boolean truth value of RH. On input $a$, the machine $M$ makes two queries: whether $(a,\text{true}) \in Q(P)$ and whether $(a,\text{false}) \in Q(P)$. It returns the logical OR of the two answers. The machine $M$ need not know whether RH is true or not.

QED.

Here we go back to the question -

Can every problem be reduced to another problem? Yes

Is that useful? No

You asked the wrong question.

The right question is

Can every problem be reduced to a “simpler” problem.

And the answer is no. First, we need to define what does it mean by simpler.

We require “simpler” to be a well-founded partial order. In particular, there is no way Problem $A$ is strictly simpler than Problem $B$ and yet Problem $B$ is strictly simpler than Problem $A$.

With a well-founded partial order, every nonempty chain has a least element, hence a lower bound. So, applying Zorn’s lemma to the reversed order, any nonempty set of problems has a minimal element.

Being minimal, it cannot be reduced into anything strictly simpler within that set.

It might be able to be reduced to an incomparable problem in that set, but that does not constitute a case of reducing to a “simpler” problem.

QED.
