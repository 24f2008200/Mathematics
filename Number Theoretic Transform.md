This diagram shows two ways of computing the same multiplication, **1234 × 5678 = 7006652**, side by side: ordinary schoolbook multiplication (top) and FFT-based multiplication using a Number Theoretic Transform, or NTT (the big block diagram).

**The schoolbook part (top)** is just what you'd do by hand: multiply 1234 by each digit of 5678, shift each partial product appropriately, and add. That addition step, before you actually carry the tens, gives the row **5 16 34 60 61 52 32** — this is the raw "convolution" of the two numbers' digits, before any carrying is applied.

**The NTT part (bottom)** computes that exact same convolution, but via pointwise multiplication instead of grade-school long multiplication — the same trick an FFT uses for fast polynomial multiplication, except everything is done in modular arithmetic (mod 337) instead of with complex numbers, which is why it's called a Number Theoretic Transform rather than a Fast Fourier Transform.

The steps:

1. **Split and zero-pad**: Each 4-digit number is treated as a degree-3 polynomial (its digits, reversed, are the coefficients): 1234 → [4,3,2,1], 5678 → [8,7,6,5]. Since multiplying two degree-3 polynomials gives a degree-6 result (7 coefficients), each is zero-padded out to length 8 (a power of 2, needed for the FFT/NTT to work) → [4,3,2,1,0,0,0,0] and [8,7,6,5,0,0,0,0].

2. **FFT (NTT) in ℤ₃₃₇**: Instead of evaluating the polynomials at complex roots of unity, they're evaluated at the 8th roots of unity *inside* the finite field ℤ₃₃₇ — that is, powers of ω₈ = 85, which satisfies ω₈⁸ ≡ 1 (mod 337). This converts each coefficient vector into a "point-value" representation: [10,329,298,126,2,271,43,301] and [26,24,298,322,2,83,43,277].

3. **Recursive pointwise multiplication (mod 337)**: This is the key trick. Multiplying two polynomials normally requires convolving their coefficients (an O(n²) operation). But in point-value form, multiplying the polynomials is just multiplying their values at each point, one at a time — O(n). This gives [260,145,173,132,4,251,164,138].

4. **Inverse FFT**: Converts the pointwise-multiplied values back from point-value form to coefficient form, recovering the convolution: [32,52,61,60,34,16,5,0]. Notice this is exactly the schoolbook's row **5 16 34 60 61 52 32**, just reversed (since the polynomial's low-degree coefficient is the ones digit).

5. **Recombination (carrying)**: These raw convolution values aren't valid digits yet (some exceed 9), so a final carrying pass — shown on the right, adding 32+52·10+61·100+... — turns them into the actual decimal number: **7006652**.

**Why bother with all this for small numbers?** For tiny inputs like 1234×5678 it's overkill — schoolbook is simpler. But schoolbook multiplication is O(n²) in the number of digits, while FFT/NTT-based multiplication is O(n log n), so for multiplying huge numbers (thousands or millions of digits, e.g. in cryptography or big-integer libraries like GMP), the NTT approach is dramatically faster. Using modular arithmetic instead of complex-number FFT also avoids floating-point rounding errors, giving an exact result — essential when you need the precise integer answer rather than an approximation.

Let's build this up from the ground, using the same numbers as before.

## The core idea: evaluation ↔ coefficients

A polynomial can be represented two equivalent ways:
- **Coefficient form**: 1234 as a polynomial is $p(x) = 4 + 3x + 2x^2 + 1x^3$ (digits reversed).
- **Point-value form**: the same polynomial, described instead by its values $p(x_0), p(x_1), \dots$ at enough distinct points $x_0, x_1, \dots$ (a degree-$d$ polynomial is uniquely determined by $d+1$ points).

Multiplying two polynomials in coefficient form means **convolving** their coefficients — that's the O(n²) work schoolbook multiplication does. But if you instead have both polynomials in **point-value form at the same points**, multiplying them is trivial: just multiply the values at each matching point, one at a time. That's what "pointwise multiplication" means — see below.

The catch: you need to convert *into* point-value form (evaluate at those points) and then *back* into coefficient form afterward (interpolate). Done naively, evaluation and interpolation are themselves O(n²), so you'd gain nothing. The FFT is a clever algorithm that does this conversion in O(n log n) instead — but only if the points you choose are the **n-th roots of unity** (numbers $\omega$ satisfying $\omega^n = 1$), because their special structure (they come in $\pm$ pairs, halve into smaller root-of-unity sets, etc.) is exactly what lets the FFT split the problem recursively in half at each step.

## Why roots of unity specifically

In ordinary FFT, the $n$-th roots of unity are complex numbers like $e^{2\pi i k/n}$. In your diagram, $n = 8$ (since the padded arrays have length 8), so you need 8 numbers $1, \omega, \omega^2, \dots, \omega^7$ with $\omega^8 = 1$, evenly spaced around the unit circle.

The **NTT** asks: can we find such "roots of unity" not on the complex circle, but inside modular arithmetic — i.e., a number $\omega$ and modulus $m$ such that $\omega^8 \equiv 1 \pmod m$, with $\omega, \omega^2, \dots, \omega^7$ all distinct? If so, everything the FFT does (the recursive splitting, evaluation, interpolation) still works, because it only relies on the algebraic properties of roots of unity — it doesn't care whether we're in $\mathbb{C}$ or in $\mathbb{Z}/m\mathbb{Z}$.

That's why "evaluating at the 8th roots of unity inside the finite field" enables multiplication: it's the same evaluate → pointwise-multiply → interpolate trick as the complex FFT, just relocated into modular arithmetic where it's exact (no rounding).

## Why mod 337

For an 8th root of unity to exist mod $m$ with all 8 powers distinct, you need $8 \mid (m-1)$ when $m$ is prime (this comes from the multiplicative group mod a prime having order $m-1$, and needing an element of order exactly 8 inside it).

Check: $337$ is prime, and $337 - 1 = 336 = 8 \times 42$. So 8 divides 336 — the group has an element of order 8, and $85$ is one: $85^8 \equiv 1 \pmod{337}$, with $85^1, \dots, 85^7$ all distinct mod 337. That's the whole reason 337 was picked — it's the smallest (or a convenient) prime making an 8th root of unity exist.

There's a second requirement: the modulus must be **big enough that no true output value gets wrapped around**. The actual convolution values here max out at 61 (well under 337), so computing mod 337 gives the exact right integers back — nothing is lost to the modular reduction. If the modulus were too small (say, mod 50), a true value like 61 would come back as $61 \bmod 50 = 11$, which is wrong. So the modulus has to be a prime that's simultaneously (a) congruent to 1 mod $n$ (so the root of unity exists) and (b) large enough to exceed every actual coefficient the convolution can produce.

## Pointwise multiplication, explicitly

After the NTT, you have:
- 1234 → $[10, 329, 298, 126, 2, 271, 43, 301]$ (its polynomial evaluated at $85^0, 85^1, \dots, 85^7 \bmod 337$)
- 5678 → $[26, 24, 298, 322, 2, 83, 43, 277]$ (same points)

"Pointwise multiplication" just means multiply corresponding entries together (mod 337):

$$10 \times 26 \bmod 337,\quad 329\times 24 \bmod 337,\quad 298\times298\bmod 337,\ \dots$$

giving $[260, 145, 173, 132, 4, 251, 164, 138]$ — 8 independent multiplications, no cross-terms, which is why this step is cheap. Compare that to coefficient-form multiplication, where computing the product's coefficient at, say, $x^3$ requires summing *several* cross-products ($a_0b_3 + a_1b_2 + a_2b_1+a_3b_0$) — that cross-term summing is exactly the convolution/O(n²) work that evaluating at roots of unity lets you skip.



Let's compute this concretely. The vector $[4,3,2,1,0,0,0,0]$ represents the polynomial

$$p(x) = 4 + 3x + 2x^2 + 1x^3$$

(the padding zeros just mean there's no $x^4, x^5, x^6, x^7$ term). The NTT evaluates $p(x)$ at the 8 powers of $\omega_8 = 85 \pmod{337}$:

**Step 1 — compute the powers of 85 mod 337** (these are the 8 "evaluation points"):

| $j$ | $85^j \bmod 337$ |
|---|---|
| 0 | 1 |
| 1 | 85 |
| 2 | 148 |
| 3 | 111 |
| 4 | 336 (≡ −1) |
| 5 | 252 |
| 6 | 189 |
| 7 | 226 |

(Sanity check: $85^4 \equiv -1$, so $85^8 \equiv 1$ — confirming 85 really does have order 8 mod 337, as it must for an 8th root of unity.)

**Step 2 — evaluate $p(x) = 4+3x+2x^2+x^3$ at each of these 8 points, mod 337:**

- $j=0,\ x=1$: $\;4+3(1)+2(1)+1 = 10$
- $j=1,\ x=85$: $\;4+3(85)+2(148)+111 = 4+255+296+111=666 \equiv 666-337 = \mathbf{329}$
- $j=2,\ x=148$: $\;x^2\equiv336,\ x^3\equiv189\;\Rightarrow\;4+444+672+189=1309\equiv1309-1011=\mathbf{298}$
- $j=3,\ x=111$: $\;x^2\equiv189,\ x^3\equiv85\;\Rightarrow\;4+333+378+85=800\equiv800-674=\mathbf{126}$
- $j=4,\ x=336\equiv-1$: $\;4-3+2-1=\mathbf{2}$
- $j=5,\ x=252$: $\;x^2\equiv148,\ x^3\equiv226\;\Rightarrow\;4+756+296+226=1282\equiv1282-1011=\mathbf{271}$
- $j=6,\ x=189$: $\;x^2\equiv336,\ x^3\equiv148\;\Rightarrow\;4+567+672+148=1391\equiv1391-1348=\mathbf{43}$
- $j=7,\ x=226$: $\;x^2\equiv189,\ x^3\equiv252\;\Rightarrow\;4+678+378+252=1312\equiv1312-1011=\mathbf{301}$

**Result:** $[10, 329, 298, 126, 2, 271, 43, 301]$ — matching the diagram exactly.

**The point:** each entry is just "plug $x = 85^j$ into the polynomial, reduce mod 337." Done this way, naively, it's 8 polynomial evaluations of degree 3 each — roughly $O(n^2)$ work overall. The actual *FFT algorithm* (Cooley–Tukey) computes this same set of 8 outputs faster, in $O(n\log n)$, by exploiting the fact that $x^2$ at position $j$ and position $j+4$ are related ($85^{j+4} \equiv -85^j$), letting it recursively split the length-8 problem into two length-4 problems, then two length-2 problems, reusing partial sums instead of recomputing everything from scratch. The brute-force evaluation above gives the same numbers, just without that speedup.


Reversing the NTT (going from point-values back to coefficients) is called **interpolation**, and it uses the same evaluate-at-roots-of-unity idea run backwards, with two small twists.

## The inverse NTT formula

If the forward transform was

$$A_j = \sum_{k=0}^{7} a_k\, \omega^{jk} \pmod{337}, \qquad \omega = 85$$

then the inverse is

$$a_k = n^{-1}\sum_{j=0}^{7} A_j\, \omega^{-jk} \pmod{337}, \qquad n = 8$$

So you need two things mod 337: the **inverse of the root** $\omega^{-1}$, and the **inverse of the length** $n^{-1} = 8^{-1}$.

## Step 1 — find $\omega^{-1} \bmod 337$

Since $\omega^8 \equiv 1$, its inverse is just $\omega^7$ (one less exponent gets you back to 1). From the earlier table, $\omega^7 = 226$. So $\omega^{-1} = 226$, and more generally $\omega^{-j} = \omega^{(8-j)\bmod 8}$ — i.e., the inverse powers are just the forward powers **read in reverse order**:

| $j$ | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 |
|---|---|---|---|---|---|---|---|---|
| $\omega^{-j}$ | 1 | 226 | 189 | 252 | 336 | 111 | 148 | 85 |

## Step 2 — find $8^{-1} \bmod 337$

Using the extended Euclidean algorithm: $337 = 42\times 8 + 1$, so $1 = 337 - 42\times 8$, meaning $8^{-1} \equiv -42 \equiv 295 \pmod{337}$. (Check: $8\times295 = 2360 = 7\times337+1$ ✓.)

## Step 3 — apply the formula

With $A = [10,329,298,126,2,271,43,301]$:

**$a_0$** (uses $\omega^{-0\cdot j}=1$ for all $j$, so it's just the sum):
$$\textstyle\sum A_j = 1380 \equiv 32 \pmod{337}, \qquad a_0 = 295\times32 = 9440 \equiv \mathbf{4}$$

**$a_1$** (uses the inverse-power table above directly):
$$\sum A_j\,\omega^{-j} = 10(1)+329(226)+298(189)+126(252)+2(336)+271(111)+43(148)+301(85) \equiv 24$$
$$a_1 = 295\times 24 = 7080 \equiv \mathbf{3}$$

**$a_2$** (now uses $\omega^{-2j}$, i.e. skip every other entry in the table — equivalently square the inverse root):
$$\sum A_j\,\omega^{-2j} \equiv 16, \qquad a_2 = 295\times16 = 4720 \equiv \mathbf{2}$$

Continuing the same way gives $a_3 = \mathbf{1}$ and $a_4=a_5=a_6=a_7=\mathbf{0}$, recovering exactly $[4,3,2,1,0,0,0,0]$ — the original padded coefficients of 1234. ✓

## The pattern

Forward and inverse NTT are **structurally the same operation** — a matrix-vector product against powers of a root of unity — just with:
- $\omega \to \omega^{-1}$ (walk the roots backward), and
- an extra overall scale factor of $n^{-1}$ at the end.

That symmetry is exactly why, in practice, the *inverse* FFT/NTT is implemented by reusing the same butterfly network as the forward one, just feeding in $\omega^{-1}$ instead of $\omega$ and dividing the final result by $n$ — no separate algorithm needed.

