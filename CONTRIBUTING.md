# Contributing

## GitHub Math Rendering Checklist

Rules for writing LaTeX math in Markdown that renders correctly on GitHub.

### 1. Display Math `$$` Must Be on Its Own Line

**Bad:**
```markdown
$$x^2 + y^2 = z^2$$
$$\begin{align*} ... \end{align*}$$
```

**Good:**
```markdown
$$
x^2 + y^2 = z^2
$$

$$
\begin{align*} ... \end{align*}
$$
```

### 2. Space Boundary Around `$` and CJK Characters

GitHub's parser requires a recognized word boundary (ASCII whitespace or ASCII punctuation) adjacent to `$` delimiters. CJK characters and fullwidth punctuation (。，：（）) are **not** recognized as valid boundaries.

**Bad:**
```markdown
所以。$x + 2$ 係          ← 。directly before $
結果是 $x^2$，然後       ← ，directly after $
```

**Good:**
```markdown
所以。 $x + 2$ 係         ← space before $
結果是 $x^2$ ，然後      ← space after $
```

### 3. No Spaces Inside `$` Delimiters

The opening `$` must not be followed by a space, and the closing `$` must not be preceded by a space.

**Bad:**
```markdown
$ x^2 $
$256 = 2^8 $
```

**Good:**
```markdown
$x^2$
$256 = 2^8$
```

### 4. Escape Underscores When a Line Has Multiple `_` Across Math

When a single line contains 2+ underscores (even across separate `$...$` expressions), GitHub's emphasis parser can match them as italic markers, destroying the math. Escape `_` as `\_` — GitHub strips the backslash before passing to LaTeX.

**Bad:**
```markdown
$\underbrace{99}_{k}\underbrace{00}_{j}$       ← 2 underscores, matched as <em>
$p'_n$ and $q'_n$ are convergents              ← 2 underscores across expressions
```

**Good:**
```markdown
$\underbrace{99}\_{k}\underbrace{00}\_{j}$
$p'\_n$ and $q'\_n$ are convergents
```

Note: A single underscore in a single expression (e.g., `$x_1$` alone on a line) is usually fine.

### 5. Use `\lbrace` / `\rbrace` for Set Braces

`\left\{` and `\right\}` can break because `\{` is ambiguous — some renderers consume the backslash as a Markdown escape before LaTeX sees it, leaving a bare `{` that `\left` cannot match.

**Bad:**
```markdown
$$
\left\{ x \ge 0 \right\}
$$
```

**Good:**
```markdown
$$
\left\lbrace x \ge 0 \right\rbrace
$$
```

### 6. Avoid Fullwidth Characters in Math

Fullwidth characters (＝, ＋, etc.) are not valid LaTeX. Use standard ASCII equivalents.

**Bad:** `$＝$`  
**Good:** `$=$`

### 7. Use `\text{...}` Instead of `\operatorname{...}`

GitHub's website rejects `\operatorname`, even though MathJax supports it. The browser displays:

```text
The following macros are not allowed: operatorname
```

For upright labels such as `ord` and `lcm`, use `\text{...}` instead.

**Bad:**
```markdown
$\operatorname{ord}(a)$
$\operatorname{lcm}(m, n)$
```

**Good:**
```markdown
$\text{ord}(a)$
$\text{lcm}(m, n)$
```

### Validation

Use the GitHub Markdown API to check that expressions are recognized as math:

```bash
curl -s -X POST https://api.github.com/markdown \
  -H "Authorization: Bearer $(gh auth token)" \
  -H "Content-Type: application/json" \
  -d '{"text":"$x_1$ and $x_2$","mode":"gfm"}'
```

Each recognized expression produces a `<math-renderer>` element. Raw `$...$` or unexpected `<em>` tags outside these elements indicate a Markdown parsing problem.

The API does not validate the website's macro restrictions or complete browser rendering. For example, it accepts `\operatorname` inside a `<math-renderer>` element, but the website rejects it. Also inspect the rendered Markdown on GitHub and check for error messages; an API-only check is not sufficient.
