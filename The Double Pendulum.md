
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

=============================================================

# Newtonian (Constraint-Force) vs Lagrangian Approach

Both describe the *same* physical system — they just choose different "coordinates" to work in, and that choice has huge downstream consequences.

## The core philosophical difference

| | **Newtonian (constraint-force)** | **Lagrangian** |
|---|---|---|
| Coordinates used | $x_1, y_1, x_2, y_2$ (Cartesian, **4** coordinates) | $\theta_1, \theta_2$ (angles, **2** coordinates) |
| Constraints | Kept explicit: $x_1^2+y_1^2=\ell_1^2$, etc. | Built into the choice of coordinates — automatically satisfied |
| Unknown forces $T_1, T_2$ | Appear explicitly, must be solved for | Never appear — eliminated by construction |
| Number of equations | 6 (4 dynamics + 2 constraints) | 2 (one per generalized coordinate) |
| What you get out | Positions **and** rod tensions | Only the motion $\theta_1(t), \theta_2(t)$ |

## Why Cartesian coordinates need constraint forces

In the picture's approach, $(x_1,y_1,x_2,y_2)$ are **not independent** — they're forced to satisfy the two rigid-rod constraints. But Newton's second law $F=ma$ doesn't know that. If you just wrote $m_1\ddot x_1 = 0$, $m_1\ddot y_1=-m_1g$ (free fall), the rod would stretch.

So you must **add unknown forces** $T_1,T_2$ along the rods, whose entire job is to enforce the constraint at every instant — pulling exactly hard enough to keep $x_1^2+y_1^2=\ell_1^2$ true, moment by moment. That's why you end up with 6 unknowns and need the 2 algebraic constraint equations to close the system. This is essentially the **method of Lagrange multipliers** applied to mechanics — $T_1, T_2$ literally play the role of multipliers enforcing the constraints.

## Why angles need no constraint forces

If instead you parametrize positions using the angles:
$$
x_1 = \ell_1\sin\theta_1, \quad y_1 = -\ell_1\cos\theta_1
$$
$$
x_2 = \ell_1\sin\theta_1 + \ell_2\sin\theta_2, \quad y_2 = -\ell_1\cos\theta_1 - \ell_2\cos\theta_2
$$

then **no matter what values** $\theta_1,\theta_2$ take, the constraints $x_1^2+y_1^2=\ell_1^2$ and $(x_2-x_1)^2+(y_2-y_1)^2=\ell_2^2$ are satisfied automatically (check: $\sin^2+\cos^2=1$). The rods can never stretch — it's geometrically impossible in this parametrization.

Since $\theta_1,\theta_2$ are *unconstrained* — they can independently take any value — you've reduced the system from 4 dependent coordinates down to **2 truly independent degrees of freedom**. This is exactly what "degrees of freedom" means: a double pendulum in a plane has 2, matching $\theta_1,\theta_2$.

## Deriving the Lagrangian equations (sketch)

1. **Kinetic energy** $T = \tfrac12 m_1(\dot x_1^2+\dot y_1^2) + \tfrac12 m_2(\dot x_2^2+\dot y_2^2)$, rewritten in terms of $\dot\theta_1,\dot\theta_2$ by differentiating the position formulas above.

2. **Potential energy** $V = m_1 g y_1 + m_2 g y_2$, also rewritten in $\theta_1,\theta_2$.

3. **Lagrangian** $\mathcal{L} = T - V$.

4. **Euler–Lagrange equations**, one per generalized coordinate:
$$
\frac{d}{dt}\left(\frac{\partial \mathcal L}{\partial \dot\theta_i}\right) - \frac{\partial \mathcal L}{\partial \theta_i} = 0, \quad i=1,2
$$

This gives you **2 second-order ODEs** in $\theta_1(t), \theta_2(t)$ directly — no tensions, no algebraic constraints, nothing extra to solve for.

## Trade-offs in practice

**Newtonian/constraint-force wins when:**
- You actually *need* the rod tensions (e.g., checking if a rod would snap, designing the physical linkage).
- You want a general-purpose numerical method that doesn't require deriving new equations for every new constraint geometry — this is how most physics engines and robotics simulators work (treat everything in Cartesian coordinates + constraints, solve the whole differential-algebraic system numerically each timestep).

**Lagrangian wins when:**
- You want the *minimal*, cleanest set of equations of motion — better for analytical work, stability analysis, chaos studies (the double pendulum's chaotic behavior is almost always studied via the 2-variable $\theta_1,\theta_2$ Lagrangian ODEs).
- You don't care about internal forces, only the trajectory.
- Adding more constraints (a third pendulum, etc.) is just "one more generalized coordinate" rather than "two more equations plus two more unknowns."

## The one-line summary

The Newtonian approach solves the constraint **by force** (literally — an unknown force that resists violation), while the Lagrangian approach solves the constraint **by choice of coordinates**, sidestepping the need for any constraint force at all. Same physics, same answer for $\theta_1(t),\theta_2(t)$ — the Lagrangian just gets there in 2 equations instead of 6.

- $T_1, T_2$ aren't given by a formula — they're determined *implicitly*, exactly as needed to keep the rods rigid at every instant.
- This is computationally messier than the Lagrangian approach (where you'd parametrize by $\theta_1, \theta_2$ only and the constraints vanish automatically), but it's more physically transparent: you can literally read off the tension in each rod at any moment, which the angle-only Lagrangian formulation hides.
- In numerical simulation, this formulation is solved as a **differential-algebraic system** (ODEs + algebraic constraints) — this is precisely the kind of system that motivates techniques like constraint stabilization or the Lagrange multiplier method in computational mechanics.
