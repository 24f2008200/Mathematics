## Theorem: The Lebesgue measure of $\mathbb{Q}$ is zero, i.e., $m(\mathbb{Q}) = 0$

This result is foundational to Lebesgue integration theory because it shows that **countable sets are "negligible"** — this is precisely why Lebesgue integration can ignore countable sets (like the rationals) without affecting the integral, whereas Riemann integration cannot even handle functions like Dirichlet's function.

---

## Setup: What we need

**Key fact:** $\mathbb{Q}$ is **countable**, i.e., there exists a bijection (or at least a surjection) $f: \mathbb{N} \to \mathbb{Q}$, so we can enumerate:
$$\mathbb{Q} = \{q_1, q_2, q_3, \ldots\}$$

**Definition (Lebesgue outer measure):** For any set $E \subseteq \mathbb{R}$,
$$m^*(E) = \inf \{ \textstyle\sum_{n=1}^{\infty} \ell(I_n) : E \subseteq \bigcup_{n=1}^{\infty} I_n,\ I_n \text{ open intervals} \}$$

We want to show $m^*(\mathbb{Q}) = 0$. Since outer measure is always $\geq 0$, it suffices to show that for **every** $\varepsilon > 0$, we can cover $\mathbb{Q}$ by intervals whose lengths sum to less than $\varepsilon$.

---

## Proof

**Step 1: Fix arbitrary $\varepsilon > 0$.**

We will construct a countable cover of $\mathbb{Q}$ by open intervals with total length $< \varepsilon$.

**Step 2: Enumerate the rationals.**

Since $\mathbb{Q}$ is countable, list its elements as:
$$\mathbb{Q} = \{q_1, q_2, q_3, \ldots\}$$

**Step 3: Cover each rational by a tiny interval.**

For each $n \in \mathbb{N}$, define the open interval centered at $q_n$:
$$I_n = \left( q_n - \frac{\varepsilon}{2^{n+2}},\ q_n + \frac{\varepsilon}{2^{n+2}} \right)$$

This interval has length:
$$\ell(I_n) = \frac{\varepsilon}{2^{n+1}}$$

**Step 4: Verify this is a valid cover.**

Since $q_n \in I_n$ for every $n$, we have:
$$\mathbb{Q} = \{q_1, q_2, \ldots\} \subseteq \bigcup_{n=1}^{\infty} I_n$$

**Step 5: Sum the lengths.**

$$\sum_{n=1}^{\infty} \ell(I_n) = \sum_{n=1}^{\infty} \frac{\varepsilon}{2^{n+1}} = \frac{\varepsilon}{2}\sum_{n=1}^\infty \frac{1}{2^n} = \frac{\varepsilon}{2} \cdot 1 = \frac{\varepsilon}{2} < \varepsilon$$

(using the geometric series $\sum_{n=1}^\infty \frac{1}{2^n} = 1$).

**Step 6: Conclude the outer measure bound.**

By definition of outer measure as an infimum over all covers:
$$m^*(\mathbb{Q}) \leq \sum_{n=1}^\infty \ell(I_n) < \varepsilon$$

**Step 7: Let $\varepsilon \to 0$.**

Since this holds for **every** $\varepsilon > 0$, and $m^*(\mathbb{Q}) \geq 0$ always, we conclude:
$$m^*(\mathbb{Q}) = 0$$

Since sets of outer measure zero are automatically Lebesgue measurable (this follows directly from the Carathéodory criterion), we get:
$$m(\mathbb{Q}) = 0 \qquad \blacksquare$$

---

## Why this matters for Lebesgue integration

This proof generalizes immediately:

> **Corollary:** *Every countable set $E \subset \mathbb{R}$ has $m(E) = 0$.*

The proof is identical — you never used any special property of $\mathbb{Q}$ beyond countability.

**The critical consequence:**

$$m(\mathbb{Q}) = 0 \implies \int_{\mathbb{Q}} f \, dm = 0 \text{ for any measurable } f$$

This is why the **Dirichlet function**
$$D(x) = \begin{cases} 1 & x \in \mathbb{Q} \\ 0 & x \notin \mathbb{Q} \end{cases}$$

is:
- **Not Riemann integrable** — every partition has intervals containing both rationals and irrationals, so upper sum $= 1$ and lower sum $= 0$ always, regardless of partition fineness.
- **Lebesgue integrable**, with $\int_{[0,1]} D \, dm = 0$, because $D = 1$ only on $\mathbb{Q} \cap [0,1]$, a set of measure zero — so it doesn't matter what $D$ does there. In Lebesgue theory, functions that agree **almost everywhere** (i.e., except on a measure-zero set) have the same integral.

This is the deep philosophical shift Lebesgue introduced: instead of partitioning the *domain* into intervals (Riemann's approach, which is destroyed by wild oscillation on dense sets), Lebesgue partitions the *range* and measures the *size* of the preimages — and "size" (measure) is robust enough to correctly assign zero weight to sets like $\mathbb{Q}$.
