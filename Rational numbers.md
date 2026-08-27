## Claim 1: Between any two rational numbers, there exists an irrational number

**Setup:** Let $a, b \in \mathbb{Q}$ with $a < b$.

**Proof:**
Since $a < b$, we have $b - a > 0$.

We know $\sqrt{2}$ is irrational, and $0 < \sqrt{2} < 2$, so:
$$0 < \frac{\sqrt{2}}{2} < 1$$

Consider the number:
$$x = a + \frac{(b-a)}{\sqrt{2}} \cdot \frac{1}{2}$$

Actually, let's use a cleaner construction. Define:
$$x = a + \frac{b - a}{\sqrt{2}}$$

Wait — we need $x < b$ too. Since $\sqrt{2} > 1$, we have $\frac{b-a}{\sqrt{2}} < b - a$, so:
$$x = a + \frac{b-a}{\sqrt{2}} < a + (b - a) = b$$

Also clearly $x > a$ since $\frac{b-a}{\sqrt{2}} > 0$.

So $a < x < b$. ✓

**Now show $x$ is irrational.** Suppose, for contradiction, $x$ is rational. Then:
$$\frac{b-a}{\sqrt{2}} = x - a$$
is rational (difference of two rationals). Since $b - a$ is rational and nonzero, we can solve:
$$\sqrt{2} = \frac{b-a}{x-a}$$

The right side is a ratio of two nonzero rationals, hence rational. But $\sqrt{2}$ is irrational — **contradiction**.

Therefore $x$ is irrational, and $a < x < b$. $\blacksquare$

---

## Claim 2: Between any two irrational numbers, there exists a rational number

**Setup:** Let $\alpha, \beta \in \mathbb{R} \setminus \mathbb{Q}$ with $\alpha < \beta$.

**Proof (using the Archimedean/density property):**

Since $\beta - \alpha > 0$, by the **Archimedean property** of $\mathbb{R}$, there exists a positive integer $n$ such that:
$$\frac{1}{n} < \beta - \alpha \quad \Longrightarrow \quad n\alpha + 1 < n\beta$$

Let $m$ be the **smallest integer** strictly greater than $n\alpha$ (this exists by the well-ordering principle applied to integers greater than $n\alpha$). By minimality of $m$:
$$m - 1 \leq n\alpha \quad \Longrightarrow \quad m \leq n\alpha + 1 < n\beta$$

Combined with $m > n\alpha$, we get:
$$n\alpha < m < n\beta$$

Dividing through by $n > 0$:
$$\alpha < \frac{m}{n} < \beta$$

Since $\frac{m}{n}$ is a ratio of integers, it is **rational**. $\blacksquare$

---

## Summary

| Statement | Key idea |
|---|---|
| Rational → Irrational exists between | Scale the gap $b-a$ by $\frac{1}{\sqrt2}$; irrationality of $\sqrt2$ forces the result to be irrational |
| Irrational → Rational exists between | Archimedean property lets you find a fraction $m/n$ with fine enough resolution to land strictly inside the interval |

Both results are special cases of a more general fact: **both $\mathbb{Q}$ and $\mathbb{R}\setminus\mathbb{Q}$ are dense in $\mathbb{R}$** — meaning every open interval $(a,b)$, no matter how small, contains infinitely many rationals *and* infinitely many irrationals.
