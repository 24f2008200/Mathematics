
# The Double Pendulum: Newtonian Formulation with Constraint Forces

This is the classic **double pendulum** — two point masses connected by rigid, massless rods, hanging from a fixed pivot. The picture shows the free-body setup with the two rod tensions $T_1, T_2$ treated as **unknown constraint forces** (like Lagrange multipliers), rather than eliminating them upfront the way you would in Lagrangian mechanics.

## Setup

- Pivot fixed at the origin.
- Rod 1 (length $\ell_1$) connects the pivot to mass $m_1$ at position $(x_1, y_1)$.
- Rod 2 (length $\ell_2$) connects $m_1$ to mass $m_2$ at position $(x_2, y_2)$.
- $\theta_1$ = angle of rod 1 from vertical, $\theta_2$ = angle of rod 2 from vertical.
- $y$ points upward, so gravity acts as $-g$ in the $y$-direction.

Since the rods swing below their pivot points, the **unit vector along each rod**, pointing from the lower mass back up toward its support, is:
$$
\hat{u}_1 = (-\sin\theta_1,\ \cos\theta_1), \qquad \hat{u}_2 = (-\sin\theta_2,\ \cos\theta_2)
$$

## Step 1 — Free body diagram for $m_2$ (the easy one)

Mass $m_2$ feels only two forces: tension $T_2$ pulling it up along the rod toward $m_1$, and gravity.

$$
m_2 \ddot{x}_2 = T_2(-\sin\theta_2) = -T_2\sin\theta_2
$$
$$
m_2 \ddot{y}_2 = T_2\cos\theta_2 - m_2 g
$$

This is exactly the third and fourth equations in the image.

## Step 2 — Free body diagram for $m_1$ (has *two* rods pulling on it)

Mass $m_1$ feels **three** forces:

1. Tension $T_1$ pulling it up toward the pivot: contributes $T_1(-\sin\theta_1, \cos\theta_1)$.
2. The **reaction** to rod 2's pull on $m_2$. Rod 2 pulls $m_2$ toward $m_1$ with force $T_2(-\sin\theta_2,\cos\theta_2)$, so by Newton's third law, rod 2 pulls $m_1$ *toward* $m_2$ with the opposite force: $-T_2(-\sin\theta_2,\cos\theta_2) = T_2(\sin\theta_2, -\cos\theta_2)$.
3. Gravity $-m_1 g$.

Summing components:
$$
m_1 \ddot{x}_1 = -T_1\sin\theta_1 + T_2\sin\theta_2
$$
$$
m_1 \ddot{y}_1 = T_1\cos\theta_1 - T_2\cos\theta_2 - m_1 g
$$

These are exactly the first two equations — note the $+T_2\sin\theta_2$ and $-T_2\cos\theta_2$ signs are the "reaction" of rod 2 pulling back on $m_1$, opposite to how it pulls on $m_2$.

## Step 3 — Why you need constraint equations at all

So far we have **4 equations** but **6 unknowns**: $x_1, y_1, x_2, y_2, T_1, T_2$. The system is underdetermined — Newton's law alone doesn't know the rods are *rigid*.

The rods enforce fixed distances, which gives two more equations:

**Rod 1 has fixed length $\ell_1$** (distance from pivot to $m_1$):
$$
x_1^2 + y_1^2 = \ell_1^2
$$

**Rod 2 has fixed length $\ell_2$** (distance from $m_1$ to $m_2$):
$$
(x_2 - x_1)^2 + (y_2 - y_1)^2 = \ell_2^2
$$

Now you have **6 equations, 6 unknowns** — a fully determined (but nonlinear, coupled) system.

## Why this matters conceptually

This is the **constraint-force** (Newtonian/Lagrange-multiplier) approach:

- $T_1, T_2$ aren't given by a formula — they're determined *implicitly*, exactly as needed to keep the rods rigid at every instant.
- This is computationally messier than the Lagrangian approach (where you'd parametrize by $\theta_1, \theta_2$ only and the constraints vanish automatically), but it's more physically transparent: you can literally read off the tension in each rod at any moment, which the angle-only Lagrangian formulation hides.
- In numerical simulation, this formulation is solved as a **differential-algebraic system** (ODEs + algebraic constraints) — this is precisely the kind of system that motivates techniques like constraint stabilization or the Lagrange multiplier method in computational mechanics.
