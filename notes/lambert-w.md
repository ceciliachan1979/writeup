# Why does $5^t = t^{625}$ have two positive real solutions?

> *Written by Claude, as an experiment in having it teach me. Not my own writing.*

A motivating tour of the Lambert W function: branches, Riemann surfaces, Lagrange inversion, and Puiseux series.

## 1. The equation, and the solution everyone spots

Consider

$$
5^t = t^{625}.
$$

The exponents $5$ and $625$ are not random: $625 = 5^4$ , and this is designed so that a clean integer solution jumps out if you notice

$$
3125 = 5^5.
$$

Try $t = 3125$ :

$$
t^{625} = (5^5)^{625} = 5^{3125}, \qquad 5^t = 5^{3125}.
$$

Both sides equal $5^{3125}$ . So $t = 3125$ is an exact solution, found purely by pattern-matching the exponent structure, no calculus needed.

The trick generalizes: whenever $t = a^{k}$ makes both sides collapse to the same power of $a$ , you get a free integer solution to $a^t = t^{a^{k-1}\cdot(\text{something})}$ -type equations. It's satisfying, but it's also a trap. It makes the equation look like it has exactly *one* natural solution and nothing more interesting to say.

## 2. Is that the only solution?

Take logarithms of both sides (everything is positive, so this is safe):

$$
t\ln 5 = 625\ln t.
$$

This is a transcendental equation in $t$ : a straight line in $t$ against a logarithm. Sketching $g(t) = t\ln 5 - 625\ln t$ mentally: as $t\to 0^+$ , $-625\ln t \to +\infty$ , so $g\to+\infty$ . At $t=3125$ , $g=0$ . For very large $t$ , the linear term wins and $g\to+\infty$ again. A function that goes $+\infty \to 0 \to \,?\to +\infty$ strongly suggests there's a dip below zero somewhere in between, meaning *two* crossings, not one. So the algebraic solution we found by inspection is almost certainly not the whole story.

## 3. What do we mean by a "branch"? (No complex numbers needed yet)

Before reaching for Lambert $W$ , it's worth understanding exactly why an inverse function can fail to be single-valued. The phenomenon is entirely visible on the ordinary real line, with no complex analysis at all.

Any function $f$ has a well-defined inverse only where it's *injective* (one-to-one): if two different inputs give the same output, there's no way to look at that output and say which input produced it. Consider $f(w) = we^w$ restricted to real $w$ . Its derivative is

$$
f'(w) = (w+1)e^{w},
$$

which is negative for $w < -1$ , zero at $w=-1$ , and positive for $w > -1$ . So $f$ decreases on $(-\infty,-1]$ and increases on $[-1,\infty)$ , with a minimum value $f(-1)=-1/e$ in between. As $w\to-\infty$ , $f(w)\to 0^-$ ; as $w\to+\infty$ , $f(w)\to+\infty$ .

Put those pieces together: for any target value $z$ strictly between $-1/e$ and $0$ , the horizontal line at height $z$ crosses the graph of $f$ **twice**, once on the decreasing piece ( $w < -1$ ) and once on the increasing piece ( $-1 < w < 0$ ). So $f$ is not injective, full stop, purely as a function of a real variable. There is no single well-defined " $w$ such that $we^w=z$ ". There are two equally valid ones.

**Branch, informally:** a branch of the inverse of a non-injective function is what you get by first restricting the domain to a piece where the function *is* injective, and then inverting that restricted piece. Different valid restrictions give different branches. Here, restricting $f$ to $[-1,\infty)$ gives one branch of the inverse, which is exactly $W_0$ , with range $[-1,\infty)$ . Restricting $f$ to $(-\infty,-1]$ gives the other, which is $W_{-1}$ , with range $(-\infty,-1]$ . Both are legitimate, both are continuous, and they agree only at the single point where the two domain pieces meet, $w=-1$ (i.e. $z=-1/e$ ).

This is the real reason $5^t=t^{625}$ can have two solutions: the underlying equation, once put in Lambert- $W$ form, asks "which $w$ satisfies $we^w=z$ ?" for a $z$ that happens to fall in the two-answer zone $(-1/e,0)$ . Nothing complex-valued is required to see this ambiguity. It's baked into the shape of $f(w)=we^w$ as a real function.

## 4. Complex branches, and the Riemann surface

If you allow $w$ to be a *complex* number, the equation $we^w=z$ gets even more solutions, infinitely many in fact, for every $z\ne0$ . Here's why: for large imaginary part, $we^w$ behaves essentially like $e^w$ , and $e^w$ is periodic in the imaginary direction ( $e^{w+2\pi i}=e^w$ ). That periodicity means the plane splits into infinitely many horizontal strips, each of which $f$ maps (almost) bijectively onto the whole complex plane. So each strip contributes one more inverse branch, called $W_k$ for integer $k$ (with $W_0$ and $W_{-1}$ being the only two that happen to land back on the real axis, matching Section 3 exactly).

Keeping track of infinitely many branches, and how they connect to each other as $z$ moves around, is what complex analysis is for. The tool that makes this precise is the [**Riemann surface**](https://en.wikipedia.org/wiki/Riemann_surface) of $W$ .

Here's the key idea, and it's worth stating plainly because it's easy to lose in the "sheets and gluing" imagery: **the whole point of a Riemann surface is to enlarge the domain**, not to do anything to the function itself. On the plain $z$ -plane, a single point $z$ genuinely is not enough information to recover $w$ . You also need to know *which branch* you meant, and that branch label was never part of the input $z$ in the first place. That's exactly why $W$ looked multivalued: it wasn't that the rule "solve $we^w=z$ " was ill-defined, it's that its domain was too small to contain everything the rule actually depends on.

So enlarge it. Instead of an input being a bare point $z$ , make an input be a *pair*: a point $z$ , together with a label saying which branch you're on (concretely: which sheet, i.e. which strip of the $w$ -plane you're standing in). That enriched set of pairs is the Riemann surface. And once "which branch" is written into the input itself, the map from (point, branch) down to $w$ is genuinely injective. Two different inputs never give the same $w$ anymore, because any ambiguity has been absorbed into the branch label. The inverse function theorem's promise, which only ever gave you injectivity in some small neighborhood, now holds globally, on the whole enlarged domain, because you engineered the domain specifically to make it true.

Picture taking infinitely many separate copies of the complex $z$ -plane, one sheet per branch $W_k$ , i.e. one sheet per possible value of that branch label, and slitting each one open along its branch cut (the boundary line where that branch would otherwise be discontinuous). Now glue the sheets to each other along those slits: the top edge of one sheet's cut is stitched to the bottom edge of the neighboring sheet's cut. The result is one connected, curled-up surface, like an infinite spiral parking ramp, on which $W$ is perfectly *single-valued*: each point of the surface carries both a $z$ -coordinate and its sheet, so it maps to exactly one $w$ . What looked like "jumping to a different branch" when you crossed a cut on a flat sheet is really just walking forward onto the next level of the ramp, still the same continuous surface, just enough of it that the branch label is baked in. A loop that circles around $z=0$ never closes back up on itself on this surface. It spirals you up (or down) onto a different sheet, exactly the way walking around $z=0$ never closes up for $\log z$ either.

You don't need to be fluent in complex analysis to take the one useful fact from this away: a Riemann surface is the enlarged domain on which a multivalued rule becomes an honest, injective function again. "Which branch am I on" stops being an extra piece of context you have to supply from outside, and becomes simply part of where you're standing.

## 5. Massaging the equation into Lambert $W$ form

Back to the problem. Our equation was

$$
t\ln 5 = 625 \ln t \quad\Longleftrightarrow\quad \frac{\ln t}{t} = \frac{\ln 5}{625}.
$$

Substitute $t = e^{s}$ , so $\ln t = s$ :

$$
s\,e^{-s} = \frac{\ln 5}{625}.
$$

This is *almost* the Lambert form, except the sign on the exponent is backwards. Multiply both sides by $-1$ and set $w = -s$ :

$$
w\,e^{w} = -\frac{\ln 5}{625} =: z.
$$

That's exactly the defining equation of $W$ . So every solution is

$$
w = W_k(z), \qquad s = -w, \qquad t = e^{s} = e^{-W_k(z)}, \qquad z = -\frac{\ln 5}{625}.
$$

Every equation of the shape $a^t = t^b$ reduces this same way to $t = e^{-W_k(-\frac{\ln a}{b})}$ . This is the general recipe, not a one-off trick for this problem.

## 6. How many real branches actually apply here?

Numerically,

$$
z = -\frac{\ln 5}{625} \approx -0.0025751.
$$

Compare against the branch point $-1/e \approx -0.367879$ . Since

$$
-\frac1e < z < 0,
$$

$z$ sits exactly in the window described in Section 3: **both** real branches, $W_0$ and $W_{-1}$ , are defined and give two *different* real values.

| branch | $W_k(z)$ | $t = e^{-W_k(z)}$ |
|---|---|---|
| $W_0$ (principal) | $-0.0025818$ | $1.0025851\ldots$ |
| $W_{-1}$ | $-8.0471896$ | $3125$ (exactly) |

So the solution everyone finds by inspection, $t=3125$ , is the **$W_{-1}$ branch**. It corresponds to $w \le -1$ , the "far" real branch. The second, easy-to-miss solution, $t \approx 1.0025851$ , is the **$W_0$ branch**, hiding just barely above $t=1$ , exactly where a casual plot at normal scale would blur it into the axis.

Sanity check without $W$ at all: near $t=1$ , write $t = 1+\varepsilon$ . Then $t\ln 5 \approx \ln 5 + \varepsilon\ln5$ and $625\ln t \approx 625\varepsilon$ . Setting them equal: $\ln 5 \approx \varepsilon(625-\ln5)$ , so $\varepsilon \approx \ln5/623.39 \approx 0.0025818$ , matching the $W_0$ value above to 4 decimal places.

## 7. Where the series actually come from

Section 3 explained *why* there are two branches. This section explains *how* to compute numbers out of each of them. The two branches, once again, need genuinely different tools, for the same reason they're different branches in the first place.

### 7a. Lagrange inversion: the power series for $W_0$

$W_0$ is well-behaved at $z=0$ (recall $f'(-1)=0$ is the only place the inverse function theorem's hypothesis fails, and $z=0$ isn't near that point on this branch), so it has an ordinary, convergent Taylor series there. The tool for extracting that series directly, without ever solving the equation algebraically, is the [**Lagrange inversion theorem**](https://en.wikipedia.org/wiki/Lagrange_inversion_theorem):

**Lagrange inversion theorem.** Suppose $w$ solves $w = z\,\varphi(w)$ for some function $\varphi$ analytic near $0$ with $\varphi(0)\ne0$ . Then the unique solution $w(z)$ with $w(0)=0$ is analytic near $z=0$ , with Taylor coefficients given directly by derivatives of $\varphi$ :

$$
w(z) = \sum_{n=1}^{\infty} \frac{z^n}{n!}\left[\frac{d^{\,n-1}}{dw^{\,n-1}}\,\varphi(w)^n\right]_{w=0}.
$$

Rearranging $we^w=z$ as $w = z\,e^{-w}$ puts it in exactly this shape, with $\varphi(w)=e^{-w}$ (and $\varphi(0)=1\ne0$ , so the theorem applies). Then $\varphi(w)^n = e^{-nw}$ , whose $(n-1)$ -th derivative is $(-n)^{n-1}e^{-nw}$ ; evaluating at $w=0$ gives $(-n)^{n-1}$ . Plugging into the formula:

$$
W_0(z) = \sum_{n=1}^{\infty}\frac{(-n)^{n-1}}{n!}\,z^n = z - z^2 + \tfrac32 z^3 - \tfrac{8}{3}z^4 + \tfrac{125}{24}z^5 - \cdots
$$

No algebraic solving of $we^w=z$ was needed anywhere. The theorem reads the coefficients straight off derivatives of $\varphi$ .

The condition $\varphi(0)\ne0$ is not a technicality: it's precisely the inverse function theorem's hypothesis in disguise, applied at $w=0$ . The theorem is guaranteed to keep working, term by term, right up to wherever that hypothesis first fails, which is exactly the branch point $w=-1$ , i.e. $z=-1/e$ . That's why the radius of convergence of this series comes out to exactly $1/e$ , no more and no less.

Plugging in $z = -0.0025751$ from Section 6:

| term | value | running sum |
|---|---|---|
| $z$ | $-0.00257510$ | $-0.00257510$ |
| $-z^2$ | $-0.00000663$ | $-0.00258173$ |
| $\tfrac32 z^3$ | $-0.0000000256$ | $-0.00258176$ |
| $-\tfrac83 z^4$ | $\approx 10^{-10}$ | $-0.00258176$ (converged) |

Three terms already give $W_0(z)\approx -0.0025818$ , hence $t=e^{0.0025818}\approx 1.00258509$ , matching Section 6 to 7 decimal places.

### 7b. Puiseux series: expanding right at the branch point

Lagrange inversion needed $\varphi(0)\ne0$ , which is another way of saying $f'(w_0)\ne0$ at the point you're inverting around. At the branch point itself, $w=-1$ , that hypothesis fails on purpose: $f'(-1)=0$ . An ordinary Taylor series in integer powers of $(z+1/e)$ cannot capture what happens here, because $f$ is not locally linear at $w=-1$ . It's locally *quadratic* (the derivative vanishes, but the second derivative doesn't). Inverting a quadratic relationship forces square roots to appear, and a series built out of fractional powers like $(z+1/e)^{1/2}$ is called a [**Puiseux series**](https://en.wikipedia.org/wiki/Puiseux_series), the natural generalization of a Taylor series to points where the inverse function theorem's hypothesis breaks down.

To find it, set $w=-1+p$ and expand $f$ around $p=0$ :

$$
f(-1+p) = (-1+p)\,e^{-1+p} = -\tfrac1e\,(1-p)\,e^{p}.
$$

Expanding $e^p = 1+p+\tfrac{p^2}2+\tfrac{p^3}6+\cdots$ and multiplying by $(1-p)$ , the linear-in- $p$ terms cancel exactly (that's the statement $f'(-1)=0$ made visible):

$$
(1-p)e^{p} = 1 - \tfrac12 p^2 - \tfrac13 p^3 - \cdots
$$

so

$$
f(-1+p) = -\tfrac1e + \tfrac1{2e}\,p^2 + \tfrac1{3e}\,p^3 + \cdots
$$

Writing $z = -\tfrac1e + \delta$ , the leading-order relationship is

$$
\delta \approx \frac{p^2}{2e} \quad\Longrightarrow\quad p \approx \pm\sqrt{2e\,\delta}.
$$

That's the Puiseux series, to leading order: a genuine half-integer power of $\delta$ , not an integer one. Translating back, with $\delta = z+1/e$ :

$$
w = -1+p \approx -1 \pm \sqrt{2 + 2ez}.
$$

The two signs are exactly the two branches meeting at the fold: $+$ gives $W_0$ , $-$ gives $W_{-1}$ , geometrically the two sheets of the Riemann surface from Section 4 glued together at this one point. (Higher-order terms, where the next correction involves $\delta^1$ , then $\delta^{3/2}$ , and so on, are found by substituting $p=\sqrt{2e\delta}\,(1+a_1\sqrt\delta+a_2\delta+\cdots)$ back into the cubic-and-higher terms above and matching order by order. The leading term alone is usually enough for a quick estimate near the fold.)

Contrast the two techniques directly: **Lagrange inversion** assumes the map is locally linear and reads off an ordinary power series from derivatives of $\varphi$ ; **Puiseux expansion** is what you fall back on exactly where that assumption fails, at a genuine fold in the function, where the natural local coordinate is $\sqrt{\delta}$ , not $\delta$ itself.

### 7c. And $W_{-1}$ near $z=0$: neither tool applies, so iterate instead

$W_{-1}$ has no power series at $z=0$ at all: not a Taylor series (the singularity there is logarithmic, not removable) and not a Puiseux series either (that machinery was specific to the fold at $-1/e$ , not to this different kind of singularity at $0$ ). Instead, the standard approach is to take logs of $we^w=z$ and solve by repeated self-substitution:

$$
W_{-1}(z) \approx \ln(-z) - \ln\big(-\ln(-z)\big) \quad (z\to0^-).
$$

Plugging in $z=-0.0025751$ : $\ln(-z)=-5.9619$ , so $-\ln(-z)=5.9619$ and $\ln(5.9619)=1.7854$ , giving

$$
W_{-1}(z)\approx -5.9619-1.7854=-7.7473,
$$

versus the true value $-8.0472$ , in the right neighborhood, refined the same way $e^x$ is refined by adding more terms of its own expansion, or simply by using this as a seed for one or two Newton iterations on $we^w=z$ , which converge quickly since $W_{-1}$ is perfectly smooth away from $z=-1/e$ .

## 8. The takeaway

The two solutions to $5^t=t^{625}$ are not a numerical curiosity. They are a direct, visible consequence of $z=-\ln5/625$ landing inside $(-1/e,\,0)$ , the one interval where the real function $f(w)=we^w$ genuinely fails to be injective (Section 3), no complex numbers required to see it. Complex analysis and the Riemann surface (Section 4) exist to organize the *infinitely many further* branches that appear once $w$ is allowed to be complex, a much bigger phenomenon than this problem needs, but the same underlying idea. And the two techniques for actually computing $W_0$ and $W_{-1}$ numerically, Lagrange inversion and Puiseux expansion, are simply the two different faces of the inverse function theorem: one for where it holds, one for where it doesn't.

| location of $z=-\frac{\ln a}{b}$ | number of real solutions |
|---|---|
| $z < -1/e$ | none (for this branch pair) |
| $z = -1/e$ | exactly one, the two branches coincide |
| $-1/e < z < 0$ | two, one from $W_0$ , one from $W_{-1}$ |

The "obvious" solution found by spotting $3125=5^5$ was never the whole answer. It was just the one solution large enough, and far enough from $t=1$ , to be visible without doing any calculus at all.

## Further reading

- [Lambert W function](https://en.wikipedia.org/wiki/Lambert_W_function) (Wikipedia): overview of the function, its branches, and applications.
- Corless, Gonnet, Hare, Jeffrey & Knuth, ["On the Lambert W Function"](https://cs.uwaterloo.ca/research/tr/1993/03/W.pdf) (1996): the original paper fixing the standard branch conventions and cuts used throughout these notes.
- [Riemann surface](https://en.wikipedia.org/wiki/Riemann_surface) (Wikipedia): the general theory behind Section 4's "enlarged domain" picture.
- [Lagrange inversion theorem](https://en.wikipedia.org/wiki/Lagrange_inversion_theorem) (Wikipedia): full statement, proof sketch, and generalizations of the tool used in Section 7a.
- [Puiseux series](https://en.wikipedia.org/wiki/Puiseux_series) (Wikipedia): the general fractional-power series framework behind Section 7b, including Puiseux's theorem for algebraic curves.
